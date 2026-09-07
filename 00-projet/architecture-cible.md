# Architecture et pile technique cible

## Vue générale

Le dashboard est une application web interne composée d'un frontend, d'un backend API, d'une base de données et de connecteurs vers Weezevent et Brevo.

```text
Navigateur utilisateur
        |
        | HTTPS après phase 3
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

## Phase 2 — périmètre SLAM

La phase 2 doit livrer :

- frontend HTML, Tailwind CSS, JavaScript vanilla ;
- backend PHP/Slim ;
- routes REST ;
- accès PostgreSQL ;
- intégration Weezevent ;
- intégration Brevo ;
- consentement et désinscription ;
- export des contacts par événement ;
- traitement des invitations lorsque les données le permettent ;
- tests end-to-end applicatifs ;
- documentation utilisateur ;
- documentation technique pour passation ;
- image Docker publiée ou exportable.

La phase 2 ne vise pas le déploiement final sur VPS.

## Phase 3 — périmètre SISR

La phase 3 prendra l'image fournie par les SLAM et devra gérer :

- VPS et accès SSH ;
- Docker / Docker Compose ;
- variables d'environnement et secrets ;
- reverse proxy ;
- HTTPS ;
- sauvegardes PostgreSQL ;
- restauration testée ;
- logs ;
- automatisations ;
- procédure de mise à jour ;
- recette technique.

## Base de données

PostgreSQL doit gérer au minimum : contacts, événements, billets ou commandes, invitations, consentements, désinscriptions, segments, synchronisations et logs.

## Sécurité minimale

- pas de clé API dans Git ;
- pas de mot de passe en clair ;
- `.env` exclu du versioning ;
- `.env.example` maintenu ;
- base non exposée publiquement après déploiement ;
- captures anonymisées si données réelles.
