# RGPD opérationnel — phase 2 SLAM

## Objectif

Le dashboard manipule des données personnelles : identité, email, téléphone éventuel, historique de billetterie, présence à des événements, invitations et segmentation marketing.

La phase 2 doit intégrer des règles concrètes dans l'application.

## Règles minimales

### Consentement

Chaque contact doit disposer d'un statut exploitable : accepté, refusé, désinscrit ou inconnu.

### Désinscription

Un contact désinscrit ne doit pas être envoyé vers Brevo dans une liste marketing.

### Conservation deux ans

La durée de conservation de deux ans est une hypothèse issue du retour client. Elle doit être validée et documentée. L'automatisation peut être reportée si nécessaire.

### Minimisation

Ne stocker que les données utiles : identité, contact, lien avec événement, billet ou invitation, consentement, historique de synchronisation.

## Preuves attendues

- champ de consentement ;
- règle d'exclusion avant envoi Brevo ;
- test d'un contact désinscrit ;
- documentation utilisateur ;
- absence de clés ou données sensibles dans Git.
