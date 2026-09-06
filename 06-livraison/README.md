# Livraison client

Cette section regroupe les documents nécessaires pour livrer le dashboard au Nice Volley Ball.

La semaine 2 ne doit pas seulement produire du code. Elle doit aboutir à un service utilisable, testable, documenté et transférable au client.

## Documents disponibles

- [Checklist de livraison](checklist-livraison.md)
- [Procédure de déploiement](procedure-deploiement.md)
- [Procédure de sauvegarde et restauration](procedure-sauvegarde-restauration.md)
- [Procédure de remise client](procedure-remise-client.md)
- [PV de recette](pv-recette.md)

## Définition de livré

Le projet peut être considéré comme livré si :

- l'application est accessible dans un environnement cible ;
- les fonctionnalités prioritaires sont démontrables ;
- la configuration est documentée ;
- les secrets sont protégés ;
- les sauvegardes existent ;
- une restauration a été testée ou, à défaut, documentée avec une réserve claire ;
- les tests principaux sont consignés ;
- le client sait comment utiliser le service ;
- les limites et réserves sont écrites ;
- les accès sont remis ou transférables.

## Responsabilités par pôle

| Pôle | Responsabilité livraison |
|---|---|
| Gestion de projet | PV, bilan, arbitrages, validation client |
| Déploiement | VPS, Docker, HTTPS, secrets, sauvegardes |
| Développement | version stable, corrections, routes et écrans |
| Test | recette, preuves, anomalies, non-régression |
| Documentation | guides utilisateur, technique et exploitation |

## Livrables minimum vendredi

- URL ou environnement cible ;
- version candidate ;
- documentation utilisateur ;
- documentation d'exploitation ;
- procédure de déploiement ;
- procédure de sauvegarde/restauration ;
- rapport de tests ;
- PV de recette ;
- liste des réserves ;
- backlog restant.
