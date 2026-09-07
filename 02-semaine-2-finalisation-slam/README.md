# Phase 2 — Finalisation applicative SLAM

## Changement d'organisation

La deuxième semaine intensive est assurée uniquement avec **5 étudiants SLAM**. Aucun étudiant SISR n'est présent pendant cette phase.

L'objectif n'est donc plus de déployer l'application sur le VPS final pendant la semaine. L'objectif est de terminer le produit côté applicatif, de le stabiliser, de le documenter et de préparer une transmission propre aux SISR.

## Objectif de la semaine

À la fin de la phase 2, les SLAM doivent fournir :

- un code applicatif terminé et stabilisé ;
- les fonctionnalités prioritaires demandées par le client ;
- un modèle de données mis à jour ;
- des tests end-to-end applicatifs ;
- une documentation utilisateur pour le client ;
- une documentation technique destinée aux SISR ;
- une image Docker complète publiée sur un registre ou exportable ;
- une liste claire des prérequis VPS et SSH à valider avec le client.

## Ce qui n'est plus attendu en phase 2

- déploiement final sur VPS ;
- configuration production complète ;
- reverse proxy définitif ;
- HTTPS définitif ;
- sauvegardes automatisées en production ;
- supervision ;
- durcissement système.

Ces éléments sont déplacés en [phase 3 — déploiement SISR](../03-phase-3-deploiement-sisr/README.md).

## Documents de cette section

- [Organisation des 5 SLAM](organisation-5-slam.md)
- [Planning jour par jour](planning-jour-par-jour.md)
- [Micro-cahier des charges V2](micro-cahier-des-charges-v2.md)
- [Matrice de traçabilité](matrice-tracabilite.md)
- [Tests et recette applicative](tests-recette-applicative.md)
- [Image Docker livrable](image-docker-livrable.md)
- [Documentation utilisateur client](documentation-utilisateur-client.md)
- [Passation technique vers les SISR](passation-sisr.md)
- [RGPD opérationnel](rgpd-operationnel.md)

## Priorité absolue

Le vendredi, il vaut mieux livrer une application plus simple mais stable, testée, dockerisée et transmissible, qu'un périmètre plus large mais fragile ou impossible à déployer ensuite.
