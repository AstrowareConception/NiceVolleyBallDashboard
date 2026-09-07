# Recette technique de déploiement

| ID | Vérification | Résultat attendu | Statut | Preuve |
|---|---|---|---|---|
| D01 | Accès SSH | connexion sécurisée possible | À faire | capture |
| D02 | Docker installé | version affichée | À faire | commande |
| D03 | Image récupérée | image présente localement | À faire | `docker images` |
| D04 | Services lancés | conteneurs up | À faire | `docker compose ps` |
| D05 | HTTPS | application accessible | À faire | capture navigateur |
| D06 | PostgreSQL privé | base non exposée publiquement | À faire | contrôle ports |
| D07 | Route santé | `/api/health` OK | À faire | curl |
| D08 | Sauvegarde | dump généré | À faire | fichier sauvegarde |
| D09 | Restauration | données restaurées | À faire | rapport |
| D10 | Logs | logs accessibles | À faire | capture |

## Livraison au client

À remettre : URL, procédure d'accès, documentation utilisateur, documentation d'exploitation, procédure de sauvegarde, procédure de restauration et procédure de mise à jour.
