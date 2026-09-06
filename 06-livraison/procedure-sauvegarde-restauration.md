# Procédure de sauvegarde et restauration

Document à compléter par le pôle déploiement et validé par le pôle test.

## 1. Données à sauvegarder

- base PostgreSQL ;
- fichiers de configuration d'exemple ;
- documentation d'exploitation ;
- logs utiles si retenus ;
- exports temporaires uniquement si nécessaire.

Les fichiers contenant des secrets ne doivent pas être copiés dans un espace non sécurisé.

## 2. Sauvegarde PostgreSQL

Exemple à adapter au nom réel du conteneur et de la base.

```bash
mkdir -p backups
DATE=$(date +%Y%m%d-%H%M%S)
docker compose exec -T db pg_dump -U nvb_user nvb_dashboard > backups/nvb-dashboard-$DATE.sql
```

## 3. Vérification de la sauvegarde

- [ ] Le fichier existe.
- [ ] Le fichier n'est pas vide.
- [ ] La date est correcte.
- [ ] Le fichier est stocké dans un emplacement prévu.
- [ ] Les droits d'accès sont contrôlés.

## 4. Restauration de test

La restauration doit idéalement être testée dans un environnement isolé.

```bash
cat backups/nvb-dashboard-YYYYMMDD-HHMMSS.sql | docker compose exec -T db psql -U nvb_user nvb_dashboard
```

## 5. Critères de réussite

- [ ] La base restaurée est accessible.
- [ ] Les tables principales sont présentes.
- [ ] Les contacts sont visibles.
- [ ] Les événements sont visibles.
- [ ] L'application redémarre correctement.
- [ ] Un parcours fonctionnel est testé après restauration.

## 6. Fréquence et conservation

À valider avec le client :

| Élément | Proposition | Décision client |
|---|---|---|
| Fréquence | quotidienne | À compléter |
| Conservation | 30 jours | À compléter |
| Emplacement | serveur + copie externe éventuelle | À compléter |
| Responsable | client ou prestataire | À compléter |

## 7. Incident de restauration

En cas d'échec :

1. ne pas écraser les anciennes sauvegardes ;
2. conserver les logs ;
3. noter l'erreur exacte ;
4. prévenir le responsable projet ;
5. documenter l'action corrective.
