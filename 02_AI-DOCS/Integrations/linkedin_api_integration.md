# API Integration: LinkedIn API

## Overview

- **API Provider:** LinkedIn
- **API Version:** Utiliser la version la plus récente et stable disponible via le programme développeur LinkedIn.
- **Documentation URL:** [Lien vers la documentation officielle de l'API LinkedIn - à insérer lors de l'implémentation]
- **Purpose:** Permettre à Linked-Pedia d'authentifier les utilisateurs, de publier et programmer des posts, de récupérer les statistiques de performance des posts et d'accéder aux données de profil nécessaires pour l'audit.

## Authentication

- **Authentication Type:** OAuth 2.0. L'utilisateur autorisera Linked-Pedia à accéder à son compte via le flux standard OAuth.
- **Credential Storage:** Les tokens d'accès et de rafraîchissement obtenus via OAuth seront stockés de manière sécurisée dans la base de données Supabase, liés à l'utilisateur. Les clés API (Client ID, Client Secret) de l'application Linked-Pedia seront stockées dans les variables d'environnement sécurisées de Supabase Edge Functions.
- **Token Management:** Implémenter un mécanisme pour rafraîchir les tokens d'accès LinkedIn avant leur expiration en utilisant le token de rafraîchissement stocké. Gérer les cas où le rafraîchissement échoue (ex: demander à l'utilisateur de reconnecter son compte).

## Key Endpoints

Les endpoints spécifiques dépendront de la version exacte de l'API utilisée, mais les fonctionnalités clés nécessiteront l'accès aux types d'endpoints suivants :

### Endpoint 1: Authentification (OAuth 2.0)

- **URL:** `https://www.linkedin.com/oauth/v2/authorization` (pour initier le flux) et `https://www.linkedin.com/oauth/v2/accessToken` (pour échanger le code contre des tokens).
- **Method:** `GET` (autorisation), `POST` (token).
- **Purpose:** Permettre à l'utilisateur d'autoriser Linked-Pedia à accéder à son compte LinkedIn et obtenir les tokens nécessaires.

#### Request Format (Authorization - Example)

```
GET https://www.linkedin.com/oauth/v2/authorization?response_type=code&client_id={your_client_id}&redirect_uri={your_redirect_uri}&state={your_state}&scope={list_of_scopes}
```

#### Request Format (Access Token - Example)

```json
POST https://www.linkedin.com/oauth/v2/accessToken
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&code={authorization_code}&redirect_uri={your_redirect_uri}&client_id={your_client_id}&client_secret={your_client_secret}
```

#### Response Format (Access Token - Example)

```json
{
  "access_token": "...",
  "expires_in": ...,
  "refresh_token": "...",
  "refresh_token_expires_in": ...,
  "scope": "...",
  "token_type": "..."
}
```

#### Error Handling

| Status Code | Meaning      | Handling Strategy                                                              |
| ----------- | ------------ | ------------------------------------------------------------------------------ |
| 400         | Bad Request  | Afficher un message d'erreur à l'utilisateur, logger les détails.              |
| 401         | Unauthorized | Indiquer que l'autorisation a échoué, guider l'utilisateur pour réessayer.     |
| 5xx         | Server Error | Afficher un message d'erreur générique, logger les détails pour investigation. |

### Endpoint 2: Profil Utilisateur

- **URL:** `https://api.linkedin.com/v2/me` (pour le profil de base), potentiellement d'autres endpoints pour des données spécifiques (éducation, expérience, etc. si l'API le permet et si nécessaire pour l'audit).
- **Method:** `GET`
- **Purpose:** Récupérer les informations du profil de l'utilisateur LinkedIn pour l'audit de profil et l'affichage dans l'application.

#### Request Format (Example)

```
GET https://api.linkedin.com/v2/me
Authorization: Bearer {access_token}
```

#### Response Format (Example - simplifié)

```json
{
  "id": "...",
  "firstName": { ... },
  "lastName": { ... },
  "profilePicture": { ... },
  "headline": "...",
  "...": "..."
}
```

#### Error Handling

| Status Code | Meaning      | Handling Strategy                                                                                                             |
| ----------- | ------------ | ----------------------------------------------------------------------------------------------------------------------------- |
| 401         | Unauthorized | Le token est invalide ou expiré. Tenter de rafraîchir le token. Si échec, demander à l'utilisateur de reconnecter son compte. |
| 403         | Forbidden    | L'application n'a pas les permissions nécessaires (scopes manquants). Afficher un message d'erreur, guider l'utilisateur.     |
| 404         | Not Found    | Profil non trouvé (improbable pour /me).                                                                                      |
| 429         | Rate Limited | Voir section Taux Limites.                                                                                                    |
| 5xx         | Server Error | Tenter une nouvelle requête après un court délai. Si échec persistant, afficher un message d'erreur, logger.                  |

### Endpoint 3: Publication et Programmation de Posts

- **URL:** `https://api.linkedin.com/v2/ugcPosts` (pour les nouveaux posts), potentiellement d'autres endpoints pour la programmation si l'API offre cette fonctionnalité directement (sinon, la programmation sera gérée côté Linked-Pedia et la publication déclenchée à l'heure prévue via l'API).
- **Method:** `POST`
- **Purpose:** Publier un nouveau post sur le profil LinkedIn de l'utilisateur ou le programmer.

#### Request Format (Example - simplifié pour un post texte)

```json
POST https://api.linkedin.com/v2/ugcPosts
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "author": "urn:li:person:{person_id}",
  "lifecycleState": "PUBLISHED", // ou "DRAFT" si l'API supporte les brouillons distants
  "specificContent": {
    "com.linkedin.ugc.ShareContent": {
      "shareCommentary": {
        "text": "Contenu du post..."
      },
      "shareMediaCategory": "NONE" // ou "IMAGE", "ARTICLE", etc.
    }
  },
  "visibility": {
    "com.linkedin.ugc.MemberNetworkVisibility": "PUBLIC" // ou "CONNECTIONS"
  }
}
```

#### Response Format (Example - simplifié)

```json
{
  "id": "urn:li:ugcPost:..."
}
```

#### Error Handling

| Status Code | Meaning      | Handling Strategy                                                                                                    |
| ----------- | ------------ | -------------------------------------------------------------------------------------------------------------------- |
| 400         | Bad Request  | Validation côté Linked-Pedia avant l'appel API. Si l'erreur persiste, afficher un message d'erreur détaillé, logger. |
| 401         | Unauthorized | Tenter de rafraîchir le token. Si échec, demander à l'utilisateur de reconnecter son compte.                         |
| 403         | Forbidden    | Permissions insuffisantes. Vérifier les scopes demandés lors de l'autorisation.                                      |
| 429         | Rate Limited | Voir section Taux Limites.                                                                                           |
| 5xx         | Server Error | Tenter une nouvelle requête. Si échec persistant, afficher un message d'erreur, logger.                              |

### Endpoint 4: Statistiques de Posts

- **URL:** Endpoints liés aux analytics ou aux posts spécifiques (la structure exacte dépend de l'API LinkedIn).
- **Method:** `GET`
- **Purpose:** Récupérer les métriques de performance (impressions, réactions, commentaires, partages) pour les posts publiés via Linked-Pedia.

#### Request Format (Example - conceptuel)

```
GET https://api.linkedin.com/v2/ugcPosts/{post_id}/analytics
Authorization: Bearer {access_token}
```

#### Response Format (Example - conceptuel)

```json
{
  "impressions": ...,
  "likes": ...,
  "comments": ...,
  "shares": ...,
  "...": "..."
}
```

#### Error Handling

| Status Code | Meaning      | Handling Strategy                                                                                                         |
| ----------- | ------------ | ------------------------------------------------------------------------------------------------------------------------- |
| 401         | Unauthorized | Tenter de rafraîchir le token. Si échec, demander à l'utilisateur de reconnecter son compte.                              |
| 403         | Forbidden    | Permissions insuffisantes.                                                                                                |
| 404         | Not Found    | Post non trouvé (peut arriver si le post est supprimé sur LinkedIn). Marquer le post comme introuvable dans Linked-Pedia. |
| 429         | Rate Limited | Voir section Taux Limites.                                                                                                |
| 5xx         | Server Error | Tenter une nouvelle requête. Si échec persistant, afficher un message d'erreur, logger.                                   |

## Rate Limits

- **Limit:** LinkedIn impose des limites de taux d'appels API. Les limites exactes dépendent du type d'application et des endpoints utilisés. [Rechercher les limites spécifiques dans la documentation officielle lors de l'implémentation].
- **Handling Strategy:** Implémenter une gestion des taux limites côté Edge Functions. Utiliser des en-têtes de réponse (si fournis par LinkedIn) pour suivre l'utilisation et attendre si nécessaire (stratégie de "retry with backoff"). Éviter les appels API inutiles.

## Data Mapping

### API to Internal Model

| API Field (Exemple)    | Internal Field (Table `posts`)         | Transformation                                  |
| ---------------------- | -------------------------------------- | ----------------------------------------------- |
| `id`                   | `linkedin_post_id`                     | Stocker l'URN complet.                          |
| `shareCommentary.text` | `content`                              | Direct.                                         |
| `lifecycleState`       | `status`                               | Mapper "PUBLISHED" à "publié", etc.             |
| `author`               | `linkedin_account_id`                  | Extraire l'ID de la personne à partir de l'URN. |
| `impressions`          | `impressions` (Table `post_analytics`) | Direct.                                         |
| `likes`                | `likes` (Table `post_analytics`)       | Direct.                                         |

### Internal Model to API

| Internal Field (Table `posts`) | API Field (Exemple)          | Transformation                                                                                     |
| ------------------------------ | ---------------------------- | -------------------------------------------------------------------------------------------------- |
| `content`                      | `shareCommentary.text`       | Direct.                                                                                            |
| `linkedin_account_id`          | `author`                     | Mapper l'ID interne à l'URN LinkedIn.                                                              |
| `status`                       | `lifecycleState`             | Mapper "publié" à "PUBLISHED", etc.                                                                |
| `scheduled_date`               | N/A (géré côté Linked-Pedia) | La programmation est gérée en interne ; l'appel API de publication est déclenché à l'heure prévue. |

## Implementation Details

- **Client Library:** Utiliser une librairie cliente TypeScript/JavaScript pour l'API LinkedIn si une est officiellement supportée ou largement adoptée et maintenue. Sinon, utiliser une librairie de requêtes HTTP standard (ex: `node-fetch` dans les Edge Functions).
- **Request Timeout:** Configurer des timeouts raisonnables pour les appels API externes (ex: 10-15 secondes).
- **Retry Strategy:** Implémenter une stratégie de retry avec backoff exponentiel pour les erreurs temporaires (ex: 429, 5xx).
- **Circuit Breaker:** Envisager un pattern Circuit Breaker pour les appels fréquents à l'API LinkedIn afin d'éviter de surcharger le système en cas de défaillance prolongée de l'API externe.

## Testing

- **Mock Server:** Utiliser un mock server (ex: MSW - Mock Service Worker) ou des mocks manuels pour simuler les réponses de l'API LinkedIn pendant le développement et les tests unitaires/d'intégration des Edge Functions.
- **Test Credentials:** Utiliser des comptes développeur LinkedIn sandbox (si disponibles) ou des comptes de test dédiés pour les tests d'intégration en environnement de staging. Gérer les identifiants de test via les variables d'environnement.
- **Key Test Cases:**
  - Flux OAuth complet (autorisation, échange de code, rafraîchissement de token).
  - Publication réussie d'un post texte, image, lien.
  - Programmation réussie d'un post.
  - Récupération des statistiques d'un post.
  - Récupération des données de profil utilisateur.
  - Gestion des erreurs (tokens expirés, taux limites, erreurs serveur).
  - Gestion des sous-comptes via l'API (si l'API permet de publier pour d'autres entités que l'utilisateur authentifié, ce qui peut nécessiter des permissions spécifiques).

## Monitoring

- **Health Checks:** Surveiller le taux d'erreur et la latence des appels aux Edge Functions qui interagissent avec l'API LinkedIn.
- **Alerting:** Configurer des alertes si le taux d'erreur des appels API LinkedIn dépasse un certain seuil ou si les temps de réponse augmentent significativement.
- **Logging:** Logger les requêtes et réponses clés (sans données sensibles) ainsi que les erreurs détaillées pour faciliter le débuggage des problèmes d'intégration.

## Fallback Strategy

En cas d'indisponibilité ou d'erreurs persistantes de l'API LinkedIn, informer l'utilisateur de l'indisponibilité temporaire des fonctionnalités affectées (publication, statistiques, audit). Les fonctionnalités ne dépendant pas de l'API (génération/optimisation de brouillons en interne, tableau de bord des brouillons) devraient rester accessibles.

## MCP Server Integration (if applicable)

- **MCP Server Name:** hdw (npx -y @horizondatawave/mcp)
- **Integration Method:** Utiliser le MCP `hdw` via l'outil `use_mcp_tool` dans les Edge Functions Supabase.
- **Key Functions:** Explorer l'utilisation des outils `search_linkedin_users`, `get_linkedin_profile`, `google_search` pour potentiellement enrichir l'audit de profil, générer des idées basées sur des profils ou des tendances publiques, ou rechercher des informations pertinentes pour la génération de contenu. L'utilisation devra être conforme aux politiques de LinkedIn et aux conditions d'utilisation du MCP.

## Security Considerations

- **Data Privacy:** Les tokens d'accès LinkedIn sont des données sensibles et doivent être traités avec le plus grand soin. Ils ne doivent jamais être exposés côté client. L'accès à ces tokens dans la base de données doit être strictement limité (RLS).
- **Compliance Requirements:** Respecter scrupuleusement les conditions d'utilisation et les politiques de développeur de LinkedIn. S'assurer que l'utilisation des données récupérées via l'API est conforme aux attentes des utilisateurs et aux réglementations applicables (ex: GDPR si pertinent).

---

_Last Updated: 22/05/2025_
