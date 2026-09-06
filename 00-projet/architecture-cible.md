# Architecture et pile technique cible

## Vue générale

Le dashboard est conçu comme une application web interne composée d'un frontend, d'un backend API, d'une base de données et de connecteurs vers les services externes.

```text
Navigateur utilisateur
        |
        | HTTPS
        v
Reverse proxy / serveur web
        |
        +--> Frontend HTML / Tailwind / JavaScript vanilla
        |
        +--> Backend PHP / Slim
                    |
                    +--> PostgreSQL
                    +--> API Weezevent
                    +--> API Brevo
```

## Frontend

Le frontend reste volontairement simple et pédagogique :

- HTML ;
- Tailwind CSS ;
- JavaScript vanilla ;
- appels API avec `fetch()` ;
- pages dashboard, événements, contacts, segments, synchronisations et administration simple.

Aucun framework frontend de type React, Vue ou Angular n'est prévu dans le périmètre pédagogique.

## Backend

Le backend est développé avec :

- PHP ;
- Slim ;
- routes REST ;
- réponses JSON ;
- accès PostgreSQL ;
- intégration Weezevent ;
- intégration Brevo ;
- logs fonctionnels ;
- gestion des erreurs.

## Base de données

Le projet utilise PostgreSQL. Le modèle doit permettre au minimum de gérer :

- contacts ;
- événements ;
- billets ou commandes ;
- invitations ;
- consentements ;
- désinscriptions ;
- segments ;
- synchronisations ;
- logs.

## Infrastructure

La phase 1 repose sur des environnements locaux dockerisés. La phase 2 vise un déploiement sur VPS.

Éléments attendus :

- Docker Compose ;
- fichier de configuration de production ;
- variables d'environnement ;
- reverse proxy ;
- HTTPS ;
- sauvegarde PostgreSQL ;
- restauration testée ;
- logs ;
- pipeline de déploiement automatique ou semi-automatique.

## Sécurité minimale

Les points suivants sont non négociables :

- pas de clé API dans Git ;
- pas de mot de passe en clair dans le dépôt ;
- `.env` exclu du versioning ;
- secrets configurés sur le serveur ou dans le pipeline ;
- accès SSH sécurisé ;
- base non exposée publiquement ;
- sauvegardes protégées ;
- données personnelles masquées dans les captures publiques.
