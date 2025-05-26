# Diagramme de Séquences : Générateur de Posts à partir de Fichier/Lien (FEAT-005)

Ce diagramme décrit le processus de génération d'un brouillon de post à partir d'un fichier téléchargé ou d'un lien fourni.

```mermaid
sequenceDiagram
    participant Utilisateur
    participant Frontend
    participant EdgeFunctionPosts
    participant SupabaseStorage
    participant ContentExtractionService
    participant AIService
    participant SupabaseDB

    Utilisateur->>Frontend: Navigue vers "Créer un Post" -> "À partir de Fichier/Lien"
    Frontend->>Utilisateur: Affiche options (télécharger fichier, saisir lien)

    alt Télécharger Fichier
        Utilisateur->>Frontend: Télécharge fichier
        Frontend->>Frontend: Affiche indicateur de progression
        Frontend->>EdgeFunctionPosts: POST /posts/generate/file (envoi fichier)
        EdgeFunctionPosts->>SupabaseStorage: Stocke fichier temporairement
        SupabaseStorage-->>EdgeFunctionPosts: Confirmation stockage
        EdgeFunctionPosts->>ContentExtractionService: Envoie fichier pour extraction de contenu
    else Saisir Lien
        Utilisateur->>Frontend: Saisit lien
        Utilisateur->>Frontend: Clique sur bouton "Générer"
        Frontend->>Frontend: Affiche indicateur de chargement
        Frontend->>EdgeFunctionPosts: POST /posts/generate/file (envoi lien)
        EdgeFunctionPosts->>ContentExtractionService: Envoie lien pour extraction de contenu (scraping)
    end

    ContentExtractionService-->>EdgeFunctionPosts: Retourne contenu extrait
    EdgeFunctionPosts->>AIService: Appelle service IA (prompt basé sur contenu extrait)
    AIService-->>EdgeFunctionPosts: Retourne contenu généré
    EdgeFunctionPosts->>SupabaseStorage: Supprime fichier temporaire (si applicable)
    SupabaseStorage-->>EdgeFunctionPosts: Confirmation suppression
    EdgeFunctionPosts-->>Frontend: Retourne contenu généré
    Frontend->>Frontend: Masque indicateur de chargement/progression
    Frontend->>Utilisateur: Affiche brouillon dans zone d'édition
    Utilisateur->>Frontend: Modifie brouillon (Optionnel)
    Utilisateur->>Frontend: Clique sur "Sauvegarder le brouillon"
    Frontend->>SupabaseDB: Insère/Met à jour post dans public.posts (statut 'brouillon')
    SupabaseDB-->>Frontend: Confirmation sauvegarde
    Frontend->>Utilisateur: Confirme sauvegarde

    alt Échec
        EdgeFunctionPosts-->>Frontend: Retourne erreur
        Frontend->>Utilisateur: Affiche message d'erreur
    end
```
