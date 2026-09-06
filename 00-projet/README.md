# Présentation générale du projet

## Objectif

Le projet **Nice Volley Ball Dashboard** vise à fournir au club un outil interne permettant de mieux exploiter les données issues de ses outils existants, principalement Weezevent et Brevo.

L'enjeu n'est pas de remplacer ces outils, mais de créer une couche de centralisation, de traitement et de pilotage permettant au club de mieux comprendre ses publics et d'agir plus efficacement.

## Problème initial

Le club dispose de plusieurs sources de données, mais celles-ci restent dispersées. Les informations de billetterie, de communication, de contacts et de suivi interne ne sont pas suffisamment croisées. Le club manque donc d'une vision consolidée sur ses spectateurs, ses nouveaux visiteurs, ses supporters réguliers, ses invités et ses contacts mobilisables pour des campagnes ciblées.

## Solution visée

Le dashboard doit permettre :

- la récupération de données depuis Weezevent ;
- le stockage structuré des événements, contacts, billets et invitations ;
- l'identification de publics utiles, par exemple les nouveaux spectateurs du dernier événement ;
- l'export ou la synchronisation de listes vers Brevo ;
- la prise en compte du consentement et de la désinscription ;
- la consultation d'indicateurs simples ;
- une exploitation sécurisée et documentée par le club.

## Documents de référence projet

- [Contexte client et besoin métier](contexte-client.md)
- [Architecture et pile technique cible](architecture-cible.md)
- [Flux métier et traitements](flux-metier.md)
- [Diagrammes Mermaid](diagrammes.md)
- [Glossaire projet](glossaire.md)

## Deux temps de réalisation

### Semaine 1 — MVP

La première semaine a permis de cadrer le besoin et de produire un premier prototype fonctionnel local. Cette phase a validé la faisabilité technique générale : récupération Weezevent, traitement de données, segmentation simple et envoi vers Brevo.

Voir : [Semaine 1 — MVP](../01-semaine-1-mvp/README.md)

### Semaine 2 — livraison

La deuxième semaine doit transformer le prototype en produit livrable : intégration des retours client, robustesse, déploiement, tests, documentation et recette.

Voir : [Semaine 2 — livraison](../02-semaine-2-livraison/README.md)

## Public pédagogique

Le projet mobilise des étudiants BTS SIO, options SLAM et SISR. Il est majoritairement orienté développement applicatif, mais il contient aussi des travaux importants d'infrastructure, de déploiement, de sécurité, de sauvegarde, d'automatisation et de documentation d'exploitation.

## Livrables globaux attendus

- micro-cahier des charges ;
- modèle de données ;
- application fonctionnelle ;
- documentation technique ;
- documentation utilisateur ;
- documentation d'exploitation ;
- environnement de déploiement ;
- plan de test et recette ;
- bilan projet ;
- preuves exploitables pour le portfolio BTS SIO.
