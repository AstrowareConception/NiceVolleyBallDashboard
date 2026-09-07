# Passation technique vers les SISR

## Objectif

Ce document doit être complété par les SLAM en fin de phase 2 afin que les SISR puissent déployer l'application en phase 3.

## Informations générales

- Dépôt applicatif : à compléter
- Branche ou tag livré : à compléter
- Image Docker : à compléter
- Tag Docker : à compléter
- Date de livraison SLAM : à compléter
- Responsable SLAM à contacter : à compléter

## Variables d'environnement

| Variable | Obligatoire | Exemple fictif | Description |
|---|---|---|---|
| APP_ENV | oui | production | environnement |
| APP_DEBUG | oui | false | erreurs détaillées |
| DB_HOST | oui | db | hôte PostgreSQL |
| DB_PORT | oui | 5432 | port PostgreSQL |
| DB_NAME | oui | nvb_dashboard | nom de base |
| DB_USER | oui | nvb_user | utilisateur base |
| DB_PASSWORD | oui | change_me | mot de passe base |
| BREVO_API_KEY | oui | change_me | clé API Brevo |
| WEEZEVENT_API_KEY | oui | change_me | clé API Weezevent |

Aucune valeur réelle ne doit être versionnée.

## À compléter

- ports exposés ;
- volumes nécessaires ;
- dossier de logs ;
- dossier d'exports ;
- migrations SQL ;
- commande de santé ;
- limites connues ;
- procédure de lancement.

## Tests de santé attendus

- application accessible ;
- base accessible ;
- route de santé OK ;
- synchronisation test ;
- export test ;
- logs écrits.
