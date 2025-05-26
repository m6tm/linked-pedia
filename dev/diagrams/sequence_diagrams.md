# Diagrammes de Séquences - Linked-Pedia

Ce document présente des diagrammes de séquences pour les flux utilisateurs clés de l'application Linked-Pedia, illustrant les interactions entre les différents composants du système (Frontend, Edge Functions, Supabase Auth, Supabase Database, API LinkedIn, Services IA/MCPs).

## 1. Flux : Inscription et Connexion Utilisateur (FEAT-001)

Ce diagramme décrit le processus d'inscription et de connexion d'un utilisateur à Linked-Pedia, en utilisant Supabase Auth.

```mermaid
sequenceDiagram
    participant Utilisateur
    participant Frontend
    participant SupabaseAuth
    participant SupabaseDB

    Utilisateur->>Frontend: Navigue vers page Inscription/Connexion
    Frontend->>Utilisateur: Affiche formulaire

    alt Inscription
        Utilisateur->>Frontend: Saisit Email/Mot de passe
        Utilisateur->>Frontend: Clique sur "S'inscrire"
        Frontend->>SupabaseAuth: Appelle signUp({email, password})
        SupabaseAuth->>SupabaseDB: Crée utilisateur dans auth.users
        SupabaseAuth-->>Frontend: Retourne succès et session
        Frontend->>SupabaseDB: Crée enregistrement dans public.users (via trigger ou appel direct)
        SupabaseDB-->>Frontend: Confirmation création public.users
        Frontend->>Utilisateur: Redirige vers Tableau de Bord
    else Connexion
        Utilisateur->>Frontend: Saisit Email/Mot de passe
        Utilisateur->>Frontend: Clique sur "Se connecter"
        Frontend->>SupabaseAuth: Appelle signInWithPassword({email, password})
        SupabaseAuth->>SupabaseDB: Vérifie identifiants
        SupabaseAuth-->>Frontend: Retourne succès et session
        Frontend->>Utilisateur: Redirige vers Tableau de Bord
    end

    alt Échec
        SupabaseAuth-->>Frontend: Retourne erreur
        Frontend->>Utilisateur: Affiche message d'erreur
    end
```

## 2. Flux : Liaison Compte LinkedIn (FEAT-002)

Ce diagramme décrit le processus de liaison d'un compte LinkedIn via OAuth 2.0.

```mermaid
sequenceDiagram
    participant Utilisateur
    participant Frontend
    participant EdgeFunctionAuth
    participant LinkedInAPI
    participant SupabaseDB

    Utilisateur->>Frontend: Navigue vers section Liaison LinkedIn
    Frontend->>Utilisateur: Affiche bouton "Lier mon compte LinkedIn"
    Utilisateur->>Frontend: Clique sur le bouton
    Frontend->>EdgeFunctionAuth: POST /auth/linkedin/connect
    EdgeFunctionAuth->>EdgeFunctionAuth: Génère URL d'autorisation (client_id, redirect_uri, state, scope)
    EdgeFunctionAuth-->>Frontend: Retourne redirect_url
    Frontend->>Utilisateur: Redirige le navigateur vers redirect_url
    Utilisateur->>LinkedInAPI: Autorise Linked-Pedia
    LinkedInAPI->>Utilisateur: Redirige le navigateur vers redirect_uri (avec code, state)
    Utilisateur->>Frontend: Arrive sur la page de callback
    Frontend->>EdgeFunctionAuth: GET /auth/linkedin/callback (avec code, state)
    EdgeFunctionAuth->>LinkedInAPI: Échange code contre tokens (access_token, refresh_token)
    LinkedInAPI-->>EdgeFunctionAuth: Retourne tokens
    EdgeFunctionAuth->>SupabaseDB: Sauvegarde tokens dans public.linkedin_accounts
    SupabaseDB-->>EdgeFunctionAuth: Confirmation sauvegarde
    EdgeFunctionAuth->>SupabaseDB: Met à jour public.users (linkedin_account_linked = true)
    SupabaseDB-->>EdgeFunctionAuth: Confirmation mise à jour
    EdgeFunctionAuth-->>Frontend: Redirige vers Frontend (ex: Tableau de Bord)
    Frontend->>Utilisateur: Affiche message succès/échec
```

## 3. Flux : Génération de Post Texte (FEAT-004)

Ce diagramme décrit la génération d'un brouillon de post texte via un service IA.

```mermaid
sequenceDiagram
    participant Utilisateur
    participant Frontend
    participant EdgeFunctionPosts
    participant AIService
    participant SupabaseDB

    Utilisateur->>Frontend: Navigue vers "Créer un Post" -> "Générer Texte"
    Frontend->>Utilisateur: Affiche formulaire de paramètres
    Utilisateur->>Frontend: Saisit mots-clés/thème
    Utilisateur->>Frontend: Clique sur "Générer"
    Frontend->>Frontend: Affiche indicateur de chargement
    Frontend->>EdgeFunctionPosts: POST /posts/generate/text (avec paramètres)
    EdgeFunctionPosts->>AIService: Appelle service IA (prompt basé sur paramètres)
    AIService-->>EdgeFunctionPosts: Retourne contenu généré
    EdgeFunctionPosts-->>Frontend: Retourne contenu généré
    Frontend->>Frontend: Masque indicateur de chargement
    Frontend->>Utilisateur: Affiche brouillon dans zone d'édition
    Utilisateur->>Frontend: Modifie brouillon (Optionnel)
    Utilisateur->>Frontend: Clique sur "Sauvegarder le brouillon"
    Frontend->>SupabaseDB: Insère/Met à jour post dans public.posts (statut 'brouillon')
    SupabaseDB-->>Frontend: Confirmation sauvegarde
    Frontend->>Utilisateur: Confirme sauvegarde
```

## 4. Flux : Audit de Profil LinkedIn (FEAT-003)

Ce diagramme décrit le processus d'audit d'un profil LinkedIn.

```mermaid
sequenceDiagram
    participant Utilisateur
    participant Frontend
    participant EdgeFunctionAnalytics
    participant LinkedInAPI
    participant SupabaseDB
    participant AIService

    Utilisateur->>Frontend: Navigue vers "Audit de Profil"
    Frontend->>Frontend: Vérifie si compte LinkedIn lié
    alt Compte lié
        Frontend->>Utilisateur: Affiche bouton "Lancer l'audit"
        Utilisateur->>Frontend: Clique sur le bouton
        Frontend->>Frontend: Affiche indicateur de chargement
        Frontend->>EdgeFunctionAnalytics: GET /analytics/profile/audit
        EdgeFunctionAnalytics->>SupabaseDB: Récupère tokens LinkedIn (via user_id)
        SupabaseDB-->>EdgeFunctionAnalytics: Retourne tokens
        EdgeFunctionAnalytics->>LinkedInAPI: Appelle API LinkedIn (récupère données profil, SSI si possible)
        LinkedInAPI-->>EdgeFunctionAnalytics: Retourne données profil
        EdgeFunctionAnalytics->>EdgeFunctionAnalytics: Calcule score d'audit (basé sur SSI et/ou données profil)
        EdgeFunctionAnalytics->>AIService: Appelle service IA (analyse données, génère recommandations)
        AIService-->>EdgeFunctionAnalytics: Retourne recommandations
        EdgeFunctionAnalytics->>SupabaseDB: Sauvegarde résultats dans public.profile_audits
        SupabaseDB-->>EdgeFunctionAnalytics: Confirmation sauvegarde
        EdgeFunctionAnalytics-->>Frontend: Retourne rapport d'audit
        Frontend->>Frontend: Masque indicateur de chargement
        Frontend->>Utilisateur: Affiche rapport d'audit
    else Compte non lié
        Frontend->>Utilisateur: Affiche message "Lier compte LinkedIn"
    end
```

## 5. Flux : Programmation de Post (FEAT-010)

Ce diagramme décrit la programmation d'un post et son déclenchement ultérieur.

```mermaid
sequenceDiagram
    participant Utilisateur
    participant Frontend
    participant EdgeFunctionSchedule
    participant SupabaseDB
    participant ScheduledTrigger
    participant EdgeFunctionPublish
    participant LinkedInAPI

    Utilisateur->>Frontend: Édite un brouillon
    Utilisateur->>Frontend: Clique sur "Programmer"
    Frontend->>Utilisateur: Affiche sélecteur date/heure
    Utilisateur->>Frontend: Sélectionne date/heure future
    Utilisateur->>Frontend: Confirme programmation
    Frontend->>EdgeFunctionSchedule: POST /posts/{id}/schedule (avec scheduled_date)
    EdgeFunctionSchedule->>SupabaseDB: Met à jour public.posts (status='programmé', scheduled_date)
    SupabaseDB-->>EdgeFunctionSchedule: Confirmation mise à jour
    EdgeFunctionSchedule-->>Frontend: Retourne succès
    Frontend->>Utilisateur: Confirme programmation, redirige vers Tableau de Bord

    ... Temps d'attente ...

    ScheduledTrigger->>SupabaseDB: Interroge public.posts (status='programmé', scheduled_date <= now)
    SupabaseDB-->>ScheduledTrigger: Retourne posts à publier
    ScheduledTrigger->>EdgeFunctionPublish: Appelle Edge Function de publication (pour chaque post)
    EdgeFunctionPublish->>SupabaseDB: Récupère contenu post et tokens LinkedIn
    SupabaseDB-->>EdgeFunctionPublish: Retourne données
    EdgeFunctionPublish->>LinkedInAPI: Appelle API LinkedIn (publie le post)
    LinkedInAPI-->>EdgeFunctionPublish: Retourne succès/échec publication
    EdgeFunctionPublish->>SupabaseDB: Met à jour public.posts (status='publié'/'échec')
    SupabaseDB-->>EdgeFunctionPublish: Confirmation mise à jour
```
