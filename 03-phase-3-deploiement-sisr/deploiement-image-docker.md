# Déploiement de l'image Docker

## À récupérer auprès des SLAM

- nom de l'image ;
- tag exact ;
- variables d'environnement ;
- ports ;
- volumes ;
- commandes de santé ;
- migrations SQL ;
- limites connues.

## Étapes de déploiement

1. créer le dossier de projet sur le VPS ;
2. créer le fichier `.env` réel ;
3. écrire ou récupérer `docker-compose.yml` ;
4. tirer l'image ;
5. lancer les services ;
6. appliquer les migrations si nécessaire ;
7. configurer le reverse proxy ;
8. activer HTTPS ;
9. exécuter les smoke tests ;
10. documenter le résultat.

## Smoke tests

- `docker compose ps` ;
- route `/api/health` ;
- accès navigateur ;
- connexion base ;
- synchronisation test ;
- export test ;
- logs consultables.
