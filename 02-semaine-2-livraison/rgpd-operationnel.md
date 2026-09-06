# RGPD opérationnel — version projet

Ce document n'est pas une consultation juridique. Il sert à cadrer les décisions techniques et fonctionnelles autour des données personnelles manipulées par le dashboard.

## Données personnelles concernées

Le projet peut manipuler :

- nom ;
- prénom ;
- email ;
- téléphone ;
- historique de billets ou présences ;
- invitations ;
- statut de consentement ;
- statut de désinscription ;
- données d'import ou de synchronisation.

## Principes à appliquer

### Minimisation

Ne stocker que les données utiles au besoin du club. Toute donnée non exploitée doit être évitée ou anonymisée.

### Finalité

Les données sont utilisées pour la gestion de la relation spectateur : suivi de présence, segmentation, communication, fidélisation et pilotage de la billetterie.

### Durée de conservation

La règle évoquée est une conservation de deux ans. Cette durée doit être validée avec le client. Elle doit être traduite techniquement par une date de dernière activité et par une procédure de purge ou d'anonymisation.

### Consentement et désinscription

Un contact désinscrit ou en opposition ne doit pas être envoyé vers Brevo. Le statut doit être visible, exploitable et testé.

### Sécurité

Les clés API, mots de passe et fichiers `.env` ne doivent jamais être versionnés. Les exports contenant des données personnelles doivent être limités, protégés et supprimés lorsqu'ils ne sont plus nécessaires.

## Règles fonctionnelles minimales

- Un contact possède un statut de consentement.
- Un contact peut être désinscrit.
- Un contact désinscrit est exclu des exports marketing et synchronisations Brevo.
- La date ou source du consentement doit être conservée si disponible.
- Les données anciennes doivent pouvoir être identifiées.
- Les captures utilisées comme preuves BTS ne doivent pas exposer inutilement de données réelles.

## Questions à valider avec le client

- Quelle est la source officielle du consentement ? Brevo, Weezevent, formulaire, saisie manuelle ?
- Le club souhaite-t-il bloquer tout envoi sans consentement explicite ou seulement exclure les désinscrits ?
- La durée de conservation de deux ans commence-t-elle à la dernière présence, au dernier achat, au dernier consentement ou à la dernière interaction ?
- Que faut-il faire après deux ans : supprimer, anonymiser, demander confirmation, archiver ?
- Qui a le droit d'exporter des contacts ?

## Tests RGPD à prévoir

| Cas | Attendu |
|---|---|
| Contact accepté | Peut être synchronisé vers Brevo |
| Contact désinscrit | Ne doit pas être synchronisé |
| Contact sans email | Ne peut pas être envoyé vers Brevo |
| Contact ancien | Doit être identifiable pour purge ou anonymisation |
| Export événement | Ne doit contenir que les champs nécessaires |
| Capture de preuve | Les données réelles doivent être masquées si nécessaire |

## Livrables associés

- règle de gestion du consentement ;
- modèle de données mis à jour ;
- test d'exclusion Brevo ;
- documentation utilisateur ;
- documentation d'exploitation ;
- note de limites et décisions à valider.
