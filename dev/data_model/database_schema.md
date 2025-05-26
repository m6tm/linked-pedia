# Structure Globale de la Base de Données - Linked-Pedia (Supabase/PostgreSQL)

Ce document décrit la structure physique proposée pour la base de données PostgreSQL dans Supabase, basée sur le modèle Entité-Relation conceptuel et les spécifications de fonctionnalités.

## 1. Schéma des Tables

### `auth.users` (Gérée par Supabase Auth)

Cette table est gérée automatiquement par Supabase Auth et stocke les informations d'authentification de base.

| Colonne              | Type                       | Contraintes | Description                       |
| -------------------- | -------------------------- | ----------- | --------------------------------- |
| `id`                 | `uuid`                     | PK          | ID unique de l'utilisateur        |
| `email`              | `text`                     | UNIQUE      | Adresse email de l'utilisateur    |
| `encrypted_password` | `text`                     |             | Mot de passe haché                |
| `created_at`         | `timestamp with time zone` |             | Date de création de l'utilisateur |
| `updated_at`         | `timestamp with time zone` |             | Date de dernière mise à jour      |
| ...                  | ...                        | ...         | Autres champs Supabase Auth       |

### `public.users`

Stocke les informations utilisateur spécifiques à l'application Linked-Pedia.

| Colonne                   | Type                       | Contraintes               | Description                                                |
| ------------------------- | -------------------------- | ------------------------- | ---------------------------------------------------------- |
| `id`                      | `uuid`                     | PK, FK -> `auth.users.id` | ID de l'utilisateur (lié à Supabase Auth)                  |
| `email`                   | `text`                     | UNIQUE, NOT NULL          | Adresse email (copie de `auth.users.email`)                |
| `created_at`              | `timestamp with time zone` | DEFAULT now()             | Date de création de l'enregistrement utilisateur           |
| `updated_at`              | `timestamp with time zone` | DEFAULT now()             | Date de dernière mise à jour                               |
| `preferences`             | `jsonb`                    | DEFAULT '{}'              | Préférences utilisateur (format JSON)                      |
| `objectives`              | `jsonb`                    | DEFAULT '{}'              | Objectifs LinkedIn de l'utilisateur (format JSON)          |
| `activity_info`           | `jsonb`                    | DEFAULT '{}'              | Informations sur l'activité de l'utilisateur (format JSON) |
| `linkedin_account_linked` | `boolean`                  | DEFAULT false, NOT NULL   | Indique si un compte LinkedIn principal est lié            |
| `assistant_status`        | `jsonb`                    | DEFAULT '{}'              | État du mode assistant pour l'utilisateur (format JSON)    |

### `public.sub_accounts`

Représente les sous-comptes gérés par un utilisateur principal.

| Colonne      | Type                       | Contraintes                       | Description                                 |
| ------------ | -------------------------- | --------------------------------- | ------------------------------------------- |
| `id`         | `uuid`                     | PK                                | ID unique du sous-compte                    |
| `user_id`    | `uuid`                     | FK -> `public.users.id`, NOT NULL | Utilisateur principal gérant ce sous-compte |
| `name`       | `text`                     | NOT NULL                          | Nom donné au sous-compte par l'utilisateur  |
| `created_at` | `timestamp with time zone` | DEFAULT now()                     | Date de création du sous-compte             |
| `updated_at` | `timestamp with time zone` | DEFAULT now()                     | Date de dernière mise à jour                |

### `public.linkedin_accounts`

Stocke les informations et tokens des comptes LinkedIn liés.

| Colonne                    | Type                       | Contraintes                       | Description                                                    |
| -------------------------- | -------------------------- | --------------------------------- | -------------------------------------------------------------- |
| `id`                       | `uuid`                     | PK                                | ID unique de la liaison compte LinkedIn                        |
| `user_id`                  | `uuid`                     | FK -> `public.users.id`, NOT NULL | Utilisateur Linked-Pedia propriétaire                          |
| `sub_account_id`           | `uuid`                     | FK -> `public.sub_accounts.id`    | ID du sous-compte si ce n'est pas le compte principal          |
| `linkedin_profile_id`      | `text`                     | UNIQUE, NOT NULL                  | ID unique du profil LinkedIn (URN)                             |
| `access_token`             | `text`                     | NOT NULL                          | Token d'accès LinkedIn (chiffré)                               |
| `refresh_token`            | `text`                     |                                   | Token de rafraîchissement LinkedIn (chiffré)                   |
| `expires_at`               | `timestamp with time zone` |                                   | Date d'expiration du token d'accès                             |
| `refresh_token_expires_at` | `timestamp with time zone` |                                   | Date d'expiration du token de rafraîchissement                 |
| `scopes`                   | `text[]`                   |                                   | Scopes accordés lors de l'autorisation                         |
| `created_at`               | `timestamp with time zone` | DEFAULT now()                     | Date de création de la liaison                                 |
| `updated_at`               | `timestamp with time zone` | DEFAULT now()                     | Date de dernière mise à jour                                   |
| `is_primary`               | `boolean`                  | DEFAULT false, NOT NULL           | Indique si c'est le compte LinkedIn principal de l'utilisateur |

### `public.posts`

Stocke les brouillons, posts programmés et publiés.

| Colonne               | Type                       | Contraintes                                   | Description                                                  |
| --------------------- | -------------------------- | --------------------------------------------- | ------------------------------------------------------------ |
| `id`                  | `uuid`                     | PK                                            | ID unique du post                                            |
| `user_id`             | `uuid`                     | FK -> `public.users.id`, NOT NULL             | Utilisateur propriétaire du post                             |
| `linkedin_account_id` | `uuid`                     | FK -> `public.linkedin_accounts.id`, NOT NULL | Compte LinkedIn associé au post                              |
| `content`             | `text`                     | NOT NULL                                      | Contenu du post                                              |
| `status`              | `text`                     | NOT NULL                                      | Statut du post ('brouillon', 'programmé', 'publié', 'échec') |
| `created_at`          | `timestamp with time zone` | DEFAULT now()                                 | Date de création du post                                     |
| `updated_at`          | `timestamp with time zone` | DEFAULT now()                                 | Date de dernière mise à jour                                 |
| `scheduled_date`      | `timestamp with time zone` |                                               | Date et heure de programmation (si programmé)                |
| `published_date`      | `timestamp with time zone` |                                               | Date et heure de publication effective (si publié)           |
| `linkedin_post_id`    | `text`                     |                                               | ID du post sur LinkedIn après publication                    |
| `metadata`            | `jsonb`                    | DEFAULT '{}'                                  | Métadonnées (type, catégorie, axes, etc.)                    |

### `public.profile_audits`

Stocke les résultats des audits de profil LinkedIn.

| Colonne               | Type                       | Contraintes                                   | Description                                                  |
| --------------------- | -------------------------- | --------------------------------------------- | ------------------------------------------------------------ |
| `id`                  | `uuid`                     | PK                                            | ID unique du rapport d'audit                                 |
| `user_id`             | `uuid`                     | FK -> `public.users.id`, NOT NULL             | Utilisateur propriétaire de l'audit                          |
| `linkedin_account_id` | `uuid`                     | FK -> `public.linkedin_accounts.id`, NOT NULL | Compte LinkedIn audité                                       |
| `audit_date`          | `timestamp with time zone` | DEFAULT now()                                 | Date et heure de l'audit                                     |
| `linkedin_ssi_score`  | `integer`                  |                                               | Score SSI si accessible via API                              |
| `calculated_score`    | `integer`                  |                                               | Score calculé par Linked-Pedia                               |
| `sections_scores`     | `jsonb`                    |                                               | Scores détaillés par section du profil                       |
| `recommendations`     | `jsonb`                    |                                               | Liste des recommandations d'optimisation                     |
| `raw_profile_data`    | `jsonb`                    |                                               | Données brutes du profil (à évaluer pour la confidentialité) |
| `status`              | `text`                     |                                               | Statut de l'audit ('completed', 'failed')                    |

### `public.post_analytics`

Stocke les statistiques de performance des posts publiés.

| Colonne        | Type                       | Contraintes                       | Description                                    |
| -------------- | -------------------------- | --------------------------------- | ---------------------------------------------- |
| `id`           | `uuid`                     | PK                                | ID unique de l'enregistrement statistique      |
| `post_id`      | `uuid`                     | FK -> `public.posts.id`, NOT NULL | Post publié associé                            |
| `impressions`  | `integer`                  | DEFAULT 0                         | Nombre d'impressions                           |
| `likes`        | `integer`                  | DEFAULT 0                         | Nombre de likes                                |
| `comments`     | `integer`                  | DEFAULT 0                         | Nombre de commentaires                         |
| `shares`       | `integer`                  | DEFAULT 0                         | Nombre de partages                             |
| `retrieved_at` | `timestamp with time zone` | DEFAULT now()                     | Date et heure de récupération des statistiques |

## 2. Index et Politiques RLS (Row Level Security)

- **Index :** Des index seront créés sur les colonnes fréquemment utilisées dans les requêtes `WHERE` ou `ORDER BY`, notamment les clés étrangères (`user_id`, `linkedin_account_id`, `post_id`, `sub_account_id`), les colonnes utilisées pour le filtrage (`status`, `is_primary`), et les colonnes de date/heure pour le tri et les déclenchements programmés (`created_at`, `scheduled_date`, `published_date`, `retrieved_at`).
- **Politiques RLS :** Des politiques RLS strictes seront implémentées sur toutes les tables `public` pour garantir que :
  - Un utilisateur ne peut accéder (lire, insérer, mettre à jour, supprimer) qu'à ses propres enregistrements dans `public.users`.
  - Un utilisateur ne peut accéder qu'aux `linkedin_accounts` qui lui appartiennent (`user_id`) ou qui sont liés à ses `sub_accounts`.
  - Un utilisateur ne peut accéder qu'aux `sub_accounts` qui lui appartiennent (`user_id`).
  - Un utilisateur ne peut accéder qu'aux `posts`, `profile_audits`, `post_analytics` associés à ses `linkedin_accounts` liés.

## 3. Considérations Techniques

- **Chiffrement :** Les tokens d'accès et de rafraîchissement LinkedIn stockés dans `public.linkedin_accounts` doivent être chiffrés au repos.
- **JSONB :** L'utilisation du type `jsonb` pour les champs `preferences`, `objectives`, `activity_info`, `sections_scores`, `recommendations`, `raw_profile_data`, `metadata`, et `assistant_status` offre de la flexibilité pour stocker des structures de données semi-structurées.
- **Synchronisation des Statistiques :** Le mécanisme de synchronisation des statistiques (FEAT-011) devra gérer l'insertion ou la mise à jour des enregistrements dans `public.post_analytics` en fonction de la date de récupération (`retrieved_at`).
- **Déclenchement Programmé :** Le mécanisme de déclenchement des posts programmés (FEAT-010) interrogera la table `public.posts` pour identifier les posts dont la `scheduled_date` est passée et le statut est 'programmé'.

Cette structure de base de données est conçue pour supporter les fonctionnalités du MVP et permettre une évolution future.
