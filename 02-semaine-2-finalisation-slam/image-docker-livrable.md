# Image Docker livrable

## Objectif

À la fin de la phase 2, les SLAM doivent fournir une application complète sous forme d'image Docker publiée sur un registre ou exportable. Cette image sera utilisée par les SISR en phase 3.

## Registre cible

Choix à trancher :

- GitHub Container Registry, par exemple `ghcr.io/astrowareconception/nice-volley-ball-dashboard` ;
- Docker Hub ;
- export avec `docker save` si aucune publication n'est possible.

## Tags recommandés

```text
v2-slam-final
v2-slam-final-YYYYMMDD
latest-slam
```

## Informations à fournir aux SISR

- nom exact de l'image ;
- tag exact ;
- ports exposés ;
- variables d'environnement obligatoires ;
- volumes nécessaires ;
- dépendance PostgreSQL ;
- commandes de lancement ;
- commande de test de santé ;
- limites connues.

## Checklist image

- [ ] image construite ;
- [ ] application démarrable ;
- [ ] aucune clé API incluse ;
- [ ] variables documentées ;
- [ ] tag documenté ;
- [ ] image publiée ou exportée ;
- [ ] commande de lancement fournie ;
- [ ] test de santé disponible.
