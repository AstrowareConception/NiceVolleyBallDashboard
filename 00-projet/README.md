# Présentation générale du projet

## Objectif

Le projet **Nice Volley Ball Dashboard** vise à fournir au club un outil interne permettant de mieux exploiter les données issues de Weezevent et Brevo.

L'enjeu n'est pas de remplacer ces outils, mais de créer une couche de centralisation, de traitement et de pilotage pour mieux comprendre les publics du club : spectateurs, nouveaux visiteurs, supporters réguliers, invités, contacts éligibles aux campagnes.

## Solution visée

Le dashboard doit permettre :

- la récupération de données depuis Weezevent ;
- le stockage structuré des événements, contacts, billets et invitations ;
- l'identification des nouveaux spectateurs ;
- l'export des contacts d'un événement ;
- l'envoi ou la synchronisation de listes vers Brevo ;
- la prise en compte du consentement et de la désinscription ;
- la consultation d'indicateurs simples ;
- une exploitation sécurisée et documentée par le club.

## Découpage en trois phases

### Phase 1 — MVP de juin

La première semaine a permis de produire un prototype fonctionnel local : récupération Weezevent, traitement de données, segmentation simple et envoi vers Brevo.

Voir : [Phase 1 — MVP](../01-semaine-1-mvp/README.md)

### Phase 2 — finalisation SLAM

La deuxième semaine est recentrée sur 5 étudiants SLAM. L'objectif est de terminer l'application, stabiliser le code, produire une image Docker et documenter l'utilisation client ainsi que la passation technique aux SISR.

Voir : [Phase 2 — finalisation SLAM](../02-semaine-2-finalisation-slam/README.md)

### Phase 3 — déploiement SISR

Les SISR prendront ensuite le relais pour installer le serveur, déployer l'image Docker, configurer HTTPS, sauvegardes, restauration, automatisations et exploitation.

Voir : [Phase 3 — déploiement SISR](../03-phase-3-deploiement-sisr/README.md)

## Livrables globaux

- application fonctionnelle ;
- image Docker publiée ou exportable ;
- documentation utilisateur client ;
- documentation technique SISR ;
- matrice de traçabilité ;
- plan de test ;
- bilan projet ;
- preuves exploitables pour le portfolio BTS SIO.
