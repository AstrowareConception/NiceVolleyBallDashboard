# Sauvegardes et automatisations

## Objectif

Préserver les données et permettre l'exploitation du service dans la durée.

## Éléments à sauvegarder

- base PostgreSQL ;
- configuration non secrète ;
- documentation d'exploitation ;
- exports générés si nécessaire.

## Restauration

Une sauvegarde non restaurée n'est pas suffisante. Il faut tester au moins une restauration dans un environnement de test.

## Automatisations possibles

- sauvegarde quotidienne ;
- suppression des anciennes sauvegardes ;
- vérification de la route de santé ;
- récupération des logs ;
- alerte en cas d'échec.

## Checklist

- [ ] script de sauvegarde écrit ;
- [ ] sauvegarde exécutée ;
- [ ] restauration testée ;
- [ ] fréquence documentée ;
- [ ] rétention documentée ;
- [ ] logs consultables ;
- [ ] automatisation planifiée.
