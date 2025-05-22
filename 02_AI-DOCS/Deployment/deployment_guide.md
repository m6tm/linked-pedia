# Deployment Guide - Link-Pedia

## Overview

- **Project:** Link-Pedia
- **Environment:** Production/Staging
- **Last Updated:** 22/05/2025

## Environment Setup

### Infrastructure

- **Hosting Provider:** Vercel (pour le frontend Next.js) et Supabase (pour le backend/base de données).
- **Region(s):** Dépend de la configuration choisie sur Vercel et Supabase. Idéalement, choisir des régions géographiquement proches pour minimiser la latence.
- **Architecture Diagram:** Voir le diagramme dans [`02_AI-DOCS/Architecture/architecture.md`](02_AI-DOCS/Architecture/architecture.md:1).

```mermaid
flowchart TD
    A[Utilisateur] --> B(Navigateur Web);
    B --> C[Frontend Next.js 15];
    C --> |Appels API| D[Supabase Edge Functions];
    C --> |SDK Auth| E[Supabase Auth];
    C --> |SDK DB| F[Supabase Database (PostgreSQL)];
    C --> |SDK Storage| G[Supabase Storage];
    D --> |Requêtes DB| F;
    D --> |Appels API| H[API LinkedIn];
    D --> |Appels API/MCP| I[Services IA (Génération, Optimisation, Tendances)];
    D --> |Appels API/MCP| J[Autres Services Tiers/MCPs];
    E --> F;

    subgraph Backend (Supabase)
        D; E; F; G
    end

    subgraph Services Externes
        H; I; J
    end

    style C fill:#D5F5E3,stroke:#333,stroke-width:2px
    style Backend fill:#AED6F1,stroke:#333,stroke-width:2px
    style Services Externes fill:#FADBD8,stroke:#333,stroke-width:2px
```

### Resources

| Resource           | Type                 | Specifications                                 | Purpose                                             |
| ------------------ | -------------------- | ---------------------------------------------- | --------------------------------------------------- |
| Frontend App       | Next.js Application  | Hébergé sur Vercel (Serverless Functions, CDN) | Servir l'interface utilisateur.                     |
| Database           | PostgreSQL Database  | Gérée par Supabase                             | Stocker les données applicatives.                   |
| Authentication     | Auth Service         | Géré par Supabase                              | Gérer les utilisateurs et les sessions.             |
| Storage            | File Storage         | Géré par Supabase                              | Stocker les fichiers téléchargés par l'utilisateur. |
| Backend Logic      | Edge Functions       | Exécutées sur l'infrastructure Supabase/Deno   | Exécuter la logique métier côté serveur.            |
| External APIs/MCPs | Third-Party Services | Dépend des services spécifiques (LinkedIn, IA) | Fournir des fonctionnalités externes.               |

## Environment Variables

Les variables d'environnement doivent être configurées à la fois sur Vercel (pour le frontend Next.js) et sur Supabase (pour les Edge Functions).

| Variable                        | Description                                          | Exemple                                  | Required |
| ------------------------------- | ---------------------------------------------------- | ---------------------------------------- | -------- |
| `NEXT_PUBLIC_SUPABASE_URL`      | URL de l'API Supabase (publique)                     | `https://your-project.supabase.co`       | Yes      |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Clé anon publique Supabase                           | `eyJ...`                                 | Yes      |
| `SUPABASE_SERVICE_ROLE_KEY`     | Clé service role Supabase (privée)                   | `eyJ...`                                 | Yes      |
| `LINKEDIN_CLIENT_ID`            | Client ID de l'application LinkedIn Developer        | `123456789`                              | Yes      |
| `LINKEDIN_CLIENT_SECRET`        | Client Secret de l'application LinkedIn Developer    | `abcdef123456`                           | Yes      |
| `LINKEDIN_REDIRECT_URI`         | URI de redirection OAuth configurée sur LinkedIn     | `https://your-app.com/api/auth/callback` | Yes      |
| `IA_SERVICE_API_KEY`            | Clé API pour le service IA externe (si utilisé)      | `xyz789`                                 | Yes      |
| `MCP_HDW_API_KEY`               | Clé API pour le MCP hdw (si utilisé)                 | `mcpkey123`                              | Yes      |
| `NODE_ENV`                      | Environnement d'exécution (development, production)  | `production`                             | Yes      |
| `APP_BASE_URL`                  | URL de base de l'application (pour les redirections) | `https://your-app.com`                   | Yes      |

## Deployment Process

### Prerequisites

- Compte Vercel lié au dépôt Git du projet.
- Projet Supabase configuré avec la base de données initialisée et les Edge Functions déployées.
- Variables d'environnement configurées sur Vercel et Supabase pour les environnements de staging et de production.
- Accès au dépôt Git du projet.
- Outils CLI installés si nécessaire (ex: Vercel CLI, Supabase CLI - bien que le déploiement via Git soit privilégié).

### Deployment Steps

Le déploiement s'effectue principalement via l'intégration Git de Vercel et Supabase.

1.  **S'assurer que le code est commité et poussé :** Toutes les modifications à déployer doivent être poussées vers la branche Git configurée pour le déploiement (ex: `main` pour la production, `develop` ou `staging` pour le staging).
2.  **Déclencher un déploiement vers l'environnement de staging :** Pousser le code vers la branche de staging. Vercel et Supabase (si configurés pour le déploiement continu depuis cette branche) construiront et déploieront automatiquement vers l'environnement de staging.
3.  **Vérifier Staging Deployment :**
    - Accéder à l'URL de l'environnement de staging.
    - Effectuer les tests d'acceptation utilisateur (UAT) définis dans le PRD (Section 6.4).
    - Exécuter des tests de fumée pour vérifier les fonctionnalités critiques (inscription, connexion, liaison LinkedIn, génération de post, tableau de bord).
    - Vérifier les logs pour détecter d'éventuelles erreurs.
4.  **Déployer vers Production :** Une fois la validation en staging terminée, fusionner la branche de staging dans la branche de production (ex: `main`). Vercel et Supabase (si configurés) construiront et déploieront automatiquement vers l'environnement de production.
5.  **Vérifier Production Deployment :**
    - Accéder à l'URL de production.
    - Effectuer des tests de fumée pour vérifier que l'application est opérationnelle en production.
    - Surveiller les métriques et les logs pour détecter tout problème post-déploiement.

### Rollback Procedure

En cas de problème critique détecté en production après un déploiement :

1.  **Identifier l'Issue :** Déterminer la cause du problème (bug, erreur de configuration, etc.) en analysant les logs et les métriques.
2.  **Exécuter Rollback :** Utiliser la fonctionnalité de rollback de la plateforme d'hébergement. Sur Vercel, cela peut impliquer de revenir à une version de déploiement précédente via le tableau de bord. Sur Supabase, cela peut nécessiter de revenir à une version précédente des Edge Functions ou d'appliquer une migration de base de données inverse si le problème est lié au schéma.
3.  **Verify Rollback :** Accéder à l'application pour confirmer que la version précédente stable est à nouveau déployée et que le problème est résolu.

## Database Migrations

Les migrations de base de données sont gérées séparément du déploiement de l'application.

### Running Migrations

Utiliser l'outil de migration de base de données associé à Supabase (ex: Supabase CLI ou un outil tiers comme Prisma si utilisé).

```bash
# Assurez-vous d'être dans le bon répertoire projet
# Exécuter les migrations en attente
supabase migration up

# Annuler la dernière migration (à utiliser avec prudence)
supabase migration down
```

### Migration Best Practices

- Toujours sauvegarder la base de données avant d'appliquer des migrations en production.
- Tester les migrations dans un environnement de staging ou de développement qui est une copie fidèle de la production.
- S'assurer que les migrations sont idempotentes (peuvent être appliquées plusieurs fois sans effets secondaires indésirables).
- Inclure des étapes de rollback (down) pour chaque migration (up).
- Versionner les fichiers de migration avec le code de l'application.

## Monitoring and Logging

### Monitoring Tools

- **Application Monitoring:** Utiliser les outils de monitoring intégrés à Vercel et Supabase. Envisager des outils tiers comme Sentry pour le suivi des erreurs.
- **Infrastructure Monitoring:** Géré par Vercel et Supabase.
- **Error Tracking:** Sentry ou un service similaire pour capturer et analyser les erreurs frontend et backend.

### Key Metrics

| Metric              | Description                                     | Alert Threshold |
| ------------------- | ----------------------------------------------- | --------------- |
| Response Time       | Temps de réponse moyen des Edge Functions       | > 500ms         |
| Error Rate          | Pourcentage de requêtes Edge Function en erreur | > 1%            |
| Database Latency    | Latence moyenne des requêtes DB Supabase        | > 100ms         |
| API Externe Latency | Latence des appels à l'API LinkedIn/Services IA | Variable        |

### Log Access

- **Application Logs:** Accessibles via le tableau de bord Vercel (pour le frontend et les fonctions serverless Vercel si utilisées) et le tableau de bord Supabase (pour les Edge Functions et la base de données).
- **Server Logs:** Gérés par Vercel et Supabase.
- **Database Logs:** Accessibles via le tableau de bord Supabase.

## Backup and Disaster Recovery

### Backup Schedule

- **Database:** Supabase gère les sauvegardes automatiques de la base de données PostgreSQL. Configurer la fréquence et la rétention selon les besoins (ex: sauvegardes quotidiennes, rétention de 30 jours).
- **User Uploads:** Supabase Storage gère le stockage des fichiers. Configurer les politiques de redondance et de sauvegarde si nécessaire.

### Disaster Recovery Procedure

En cas de sinistre majeur affectant la base de données ou le stockage :

1.  **Assess the Situation :** Déterminer l'étendue des dégâts et les données affectées.
2.  **Restore from Backup :** Utiliser les outils de restauration de Supabase pour restaurer la base de données et le stockage à partir de la dernière sauvegarde valide.
3.  **Verify Restoration :** Vérifier l'intégrité des données restaurées et s'assurer que l'application fonctionne correctement avec les données restaurées.

## Security Considerations

- **SSL/TLS:** Géré automatiquement par Vercel et Supabase pour toutes les communications.
- **Firewall Rules:** Gérées par Vercel et Supabase. Configurer les règles d'accès à la base de données pour n'autoriser que les connexions depuis les Edge Functions et les adresses IP de confiance (si applicable).
- **Access Control:** Géré par Supabase Auth et les politiques RLS.
- **Secret Management:** Utiliser les variables d'environnement sécurisées fournies par Vercel et Supabase pour stocker les clés API et autres informations sensibles.

## Performance Optimization

- **Caching Strategy:** Utiliser le caching de Next.js (Data Cache, Full Route Cache) et le CDN de Vercel. Envisager le caching des réponses des services externes dans les Edge Functions si approprié.
- **CDN Configuration:** Géré automatiquement par Vercel.
- **Database Optimization:** Optimiser les requêtes SQL, utiliser des index appropriés, surveiller les performances de la base de données via le tableau de bord Supabase.

## Troubleshooting

### Common Issues

#### Issue 1: Échec de la liaison du compte LinkedIn

- **Symptoms:** L'utilisateur ne parvient pas à connecter son compte LinkedIn. Message d'erreur affiché dans l'application.
- **Cause:** Erreur dans le flux OAuth (redirect URI incorrecte, scopes manquants), clés API LinkedIn invalides, problème temporaire avec l'API LinkedIn.
- **Resolution:** Vérifier la configuration des clés API et de l'URI de redirection sur LinkedIn Developer Portal et dans les variables d'environnement. Vérifier les logs des Edge Functions gérant le callback OAuth. S'assurer que les scopes demandés sont corrects. Vérifier le statut de l'API LinkedIn.

#### Issue 2: Échec de publication/programmation d'un post

- **Symptoms:** Le post ne s'affiche pas sur LinkedIn à l'heure prévue ou immédiatement. Le statut dans Link-Pedia passe à 'échec'.
- **Cause:** Token d'accès LinkedIn expiré ou invalide, permissions insuffisantes, contenu du post rejeté par LinkedIn, problème temporaire avec l'API LinkedIn, erreur dans l'Edge Function de publication.
- **Resolution:** Vérifier le statut de liaison du compte LinkedIn de l'utilisateur. Vérifier les logs de l'Edge Function de publication pour les messages d'erreur de l'API LinkedIn. S'assurer que le token d'accès est valide et rafraîchi correctement. Vérifier le contenu du post par rapport aux politiques de contenu de LinkedIn.

#### Issue 3: Suggestions IA non générées ou non pertinentes

- **Symptoms:** La génération ou l'optimisation de post échoue ou les suggestions sont de mauvaise qualité.
- **Cause:** Problème avec le service IA externe/MCP, clés API IA invalides, erreur dans l'Edge Function appelant le service IA, paramètres de requête IA incorrects.
- **Resolution:** Vérifier le statut du service IA externe/MCP. Vérifier les logs de l'Edge Function appelant le service IA. S'assurer que les clés API IA sont correctes. Vérifier le format et les paramètres envoyés au service IA.

## Contacts and Escalation

| Role                | Name           | Contact         | When to Contact                                                         |
| ------------------- | -------------- | --------------- | ----------------------------------------------------------------------- |
| Utilisateur         | [Votre Nom/ID] | [Votre Contact] | Pour toute validation, feedback, ou problème non résolu par l'Agent IA. |
| Agent IA            | Roo            | [Canal actuel]  | Pour les propositions, questions, rapports d'erreur.                    |
| Support Supabase    | N/A            | Documentation   | Problèmes liés à l'infrastructure Supabase.                             |
| Support Vercel      | N/A            | Documentation   | Problèmes liés à l'hébergement Vercel.                                  |
| Support Services IA | N/A            | Documentation   | Problèmes liés aux services IA externes/MCPs.                           |

---

_This document should be reviewed and updated before each deployment._
