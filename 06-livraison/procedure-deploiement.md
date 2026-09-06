# Procédure de déploiement

Document à compléter par le pôle déploiement.

## 1. Environnement cible

| Élément | Valeur |
|---|---|
| Fournisseur VPS | À compléter |
| Propriétaire du compte | À compléter |
| Nom de domaine ou sous-domaine | À compléter |
| Système d'exploitation | À compléter |
| Accès SSH | À compléter sans secret |
| Répertoire applicatif | À compléter |

## 2. Pré-requis serveur

- utilisateur non root dédié ;
- accès SSH sécurisé par clé ;
- pare-feu configuré ;
- Docker installé ;
- Docker Compose disponible ;
- ports nécessaires ouverts ;
- clés API configurées hors dépôt ;
- sauvegardes prévues.

## 3. Variables d'environnement

Les secrets ne doivent jamais être écrits dans ce document.

| Variable | Rôle | Où la configurer |
|---|---|---|
| `APP_ENV` | environnement | serveur ou secrets CI/CD |
| `APP_URL` | URL publique | serveur ou secrets CI/CD |
| `DB_HOST` | hôte PostgreSQL | `.env` serveur |
| `DB_NAME` | base | `.env` serveur |
| `DB_USER` | utilisateur base | `.env` serveur |
| `DB_PASSWORD` | mot de passe base | secret |
| `BREVO_API_KEY` | clé Brevo | secret |
| `WEEZEVENT_API_KEY` | clé Weezevent | secret |

## 4. Déploiement manuel de référence

À adapter au dépôt applicatif réel.

```bash
git pull origin main
docker compose -f docker-compose.prod.yml pull
docker compose -f docker-compose.prod.yml up -d --build
docker compose -f docker-compose.prod.yml ps
```

## 5. Déploiement automatisé ou semi-automatisé

Le pipeline doit au minimum :

- récupérer la version validée ;
- vérifier que les secrets nécessaires existent ;
- lancer les tests ou vérifications disponibles ;
- construire ou redémarrer les services ;
- exécuter les migrations si nécessaire ;
- effectuer un smoke test ;
- conserver une trace d'exécution.

## 6. Smoke tests après déploiement

- [ ] URL accessible.
- [ ] Page de connexion accessible.
- [ ] API healthcheck OK.
- [ ] Base accessible depuis l'application.
- [ ] Synchronisation Weezevent testée ou simulée.
- [ ] Export événement testé.
- [ ] Brevo testé avec contact de test.
- [ ] Logs consultables.

## 7. Retour arrière

Décrire ici comment revenir à la version précédente : tag Git, image Docker précédente, restauration de base si nécessaire.

## 8. Réserves éventuelles

Lister ici les éléments non finalisés ou dépendants du client.
