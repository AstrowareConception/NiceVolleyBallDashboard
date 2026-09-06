# Matrice de traçabilité — besoins, fonctionnalités, tests, preuves

Cette matrice permet de transformer les retours client en tâches vérifiables. Elle doit être complétée lundi matin puis mise à jour chaque jour.

## Règle d'utilisation

Chaque ligne doit pouvoir répondre à six questions :

1. Quel besoin client traite-t-on ?
2. Quelle fonctionnalité répond à ce besoin ?
3. Quel écran ou quelle route API est concerné ?
4. Quelles données sont nécessaires ?
5. Quel test permet de valider ?
6. Quelle preuve sera conservée ?

## Matrice initiale

| ID | Besoin client | Priorité | Fonctionnalité | Écran / API | Données concernées | Test attendu | Pôle responsable | Statut |
|---|---|---|---|---|---|---|---|---|
| B01 | Exporter les contacts d'un événement | Must | Export événement | Écran événement, `GET /events/{id}/contacts/export` | événements, contacts, billets | CSV généré avec contacts attendus | Développement / Test | À faire |
| B02 | Identifier les nouveaux spectateurs du dernier événement | Must | Segment nouveaux spectateurs | Dashboard, `GET /events/{id}/new-spectators` | contacts, historique billets | Le contact déjà connu est exclu, le nouveau est inclus | Développement / Test | À faire |
| B03 | Retrouver les invitations | Should | Filtre invitations | Écran événement, `GET /events/{id}/invitations` | billets, type billet, montant, code | Les invitations sont distinguées des billets payants | Migration / Dév | À confirmer |
| B04 | Savoir si une invitation a été utilisée | Should | Statut invitation utilisée/non utilisée | Écran événement | billets, scan, présence | Invitation scannée = utilisée ; non scannée = non utilisée | Dév / Test | Dépend données |
| B05 | Gérer la désinscription email | Must | Statut désinscrit | Fiche contact, sync Brevo | contact, consentement | Un contact désinscrit est exclu de Brevo | Développement / Test | À faire |
| B06 | Prendre en compte le consentement | Must | Filtrage des contacts éligibles | Sync Brevo, export | consentement, source, date | Seuls les contacts autorisés sont envoyés selon la règle retenue | Migration / Dév | À faire |
| B07 | Conserver les données pendant une durée limitée | Should | Règle de conservation | Tâche maintenance, documentation | dates, contacts, billets | Les données anciennes sont listées, purge ou anonymisation proposée | Migration / SISR | À cadrer |
| B08 | Informer avant suppression ou expiration | Could | Alerte ou email avant purge | Écran admin ou tâche future | contact, email, date limite | Liste des contacts concernés générée | Dév | Optionnel |
| B09 | Livrer le service au client | Must | Déploiement VPS | Pipeline, reverse proxy, HTTPS | configuration, secrets | Service accessible, smoke test OK | Déploiement | À faire |
| B10 | Protéger les clés Weezevent et Brevo | Must | Gestion des secrets | `.env`, secrets CI/CD | clés API | Aucune clé dans Git, variables présentes sur serveur | Déploiement / Test | À faire |
| B11 | Assurer sauvegarde et restauration | Must | Backup PostgreSQL | scripts, documentation | base PostgreSQL | Sauvegarde créée puis restaurée en test | SISR / Test | À faire |
| B12 | Documenter l'utilisation | Must | Guide utilisateur | documentation | parcours utilisateur | Un utilisateur peut suivre la procédure | Test / Documentation | À faire |
| B13 | Documenter l'exploitation | Must | Guide exploitation | documentation | serveur, Docker, logs | Un technicien peut relancer et diagnostiquer | Déploiement / Test | À faire |
| B14 | Valider avec le client | Must | Recette finale | PV de recette | fonctionnalités livrées | Démo complète et réserves tracées | Gestion projet / Test | À faire |

## Statuts possibles

- À faire
- En cours
- À tester
- Validé
- Bloqué
- Reporté
- Hors périmètre

## Critère de fermeture d'une ligne

Une ligne ne peut être fermée que si :

- le besoin est traité ou explicitement reporté ;
- le test associé est exécuté ;
- le résultat est consigné ;
- une preuve existe ;
- les limites sont indiquées si la fonctionnalité est partielle.
