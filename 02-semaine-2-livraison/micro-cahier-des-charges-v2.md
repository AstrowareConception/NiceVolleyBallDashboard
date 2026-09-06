# Micro-cahier des charges V2 — à compléter lundi matin

## 1. Objectif de la version V2

Transformer le MVP validé en une version livrable au client, intégrant les retours prioritaires, un déploiement exploitable, une documentation claire et des tests de recette.

## 2. Périmètre fonctionnel prioritaire

### F1 — Export des contacts d'un événement

L'utilisateur doit pouvoir sélectionner un événement et exporter l'ensemble des contacts associés.

Critères d'acceptation :

- un événement peut être sélectionné ;
- les contacts liés à l'événement sont affichés ;
- un export est généré ;
- le fichier contient les champs attendus ;
- le fonctionnement est testé sur un jeu de données connu.

### F2 — Nouveaux spectateurs du dernier événement

L'utilisateur doit pouvoir identifier les contacts venus pour la première fois lors d'un événement donné.

Critères d'acceptation :

- la règle de calcul est documentée ;
- le résultat est affiché ;
- un jeu de test permet de vérifier le calcul ;
- la liste peut être exploitée pour Brevo si le consentement le permet.

### F3 — Invitations et tickets offerts

Le système doit permettre d'identifier les invitations ou tickets offerts lorsque les données Weezevent permettent de les distinguer.

Critères d'acceptation :

- la règle d'identification des invitations est documentée ;
- les invitations sont visibles par événement ;
- la présence ou absence est indiquée si disponible ;
- les limites liées aux données Weezevent sont explicitées.

### F4 — Consentement et désinscription

Le système doit empêcher l'envoi vers Brevo des contacts désinscrits ou n'ayant pas le consentement requis selon la règle retenue.

Critères d'acceptation :

- le statut de consentement est stocké ;
- le statut est visible ou exploitable ;
- un contact désinscrit est exclu d'un export ou envoi Brevo ;
- le comportement est testé.

### F5 — Déploiement livrable

Le service doit être déployé ou prêt à être déployé dans un environnement cible transférable au client.

Critères d'acceptation :

- le VPS ou l'environnement cible est documenté ;
- la procédure de déploiement est écrite ;
- les secrets sont hors Git ;
- le service est accessible ou reproductible ;
- les sauvegardes sont prévues et testées.

## 3. Contraintes

- Application web interne.
- Frontend HTML, Tailwind CSS, JavaScript vanilla.
- Backend PHP/Slim.
- Base PostgreSQL.
- Environnement Docker.
- Données personnelles protégées.
- Clés API hors dépôt.
- Livraison documentée.

## 4. Priorisation MoSCoW

### Must

- Déploiement ou préproduction exploitable.
- Export contacts d'un événement.
- Gestion du consentement et exclusion Brevo.
- Tests end-to-end des parcours principaux.
- Documentation utilisateur et exploitation.

### Should

- Invitations et tickets offerts.
- Présence réelle si disponible.
- Sauvegarde/restauration testée.
- Historique de synchronisation.
- Logs exploitables.

### Could

- Email de rappel avant expiration de conservation.
- Tableau d'administration RGPD.
- Supervision légère.
- Graphiques avancés.

### Won't

- CRM complet.
- Refonte du site public.
- Application mobile.
- Paiement intégré.
- Connexion FFVB réelle.

## 5. Questions à trancher lundi

- Quelles données réelles peuvent être utilisées ?
- Quelles données doivent rester fictives ou anonymisées ?
- Quelle est la source du consentement ?
- Comment reconnaître une invitation dans Weezevent ?
- Le VPS est-il disponible et à quel nom de domaine ?
- Qui possède les accès définitifs côté client ?
- Quel scénario de recette sera présenté vendredi ?
