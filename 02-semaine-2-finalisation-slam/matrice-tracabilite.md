# Matrice de traçabilité — phase 2 SLAM

| Besoin client | Fonctionnalité | Écran | API / traitement | Données concernées | Test attendu | Preuve | Statut |
|---|---|---|---|---|---|---|---|
| Récupérer les données Weezevent | Synchronisation Weezevent | Synchronisation | `POST /sync/weezevent` | événements, billets, contacts | Synchronisation sur jeu connu | capture + log | À faire |
| Exporter les contacts d'un match | Export événement | Détail événement | `GET /events/{id}/contacts/export` | événement, contacts, billets | CSV conforme | fichier export | À faire |
| Identifier les nouveaux spectateurs | Segment nouveaux spectateurs | Dashboard / événement | requête première participation | contacts, billets | nouveau inclus, ancien exclu | jeu + capture | À faire |
| Retrouver les invitations | Liste invitations | Événement | filtre type invitation | billets, invitations | invitations visibles | capture | À vérifier |
| Voir les invitations utilisées | Présence invitation | Événement | statut présence si disponible | billets, contrôle accès | présent/absent affiché | test | À vérifier |
| Gérer la désinscription | Exclusion Brevo | Fiche contact / Brevo | filtre avant envoi | contacts, consentements | désinscrit ignoré | test | À faire |
| Respecter le RGPD | Consentement + conservation | Fiche contact / admin | règle métier | contacts, logs | règle visible et testée | doc + capture | À faire |
| Livrer sans SISR | Image Docker | Documentation | build image | code + env | lancement depuis image | tag + commandes | À faire |
| Préparer phase 3 | Passation SISR | Documentation | procédure | image, env, ports | procédure exploitable | doc | À faire |

## Statuts possibles

À faire, En cours, Bloqué, À tester, Validé, Reporté phase 3, Abandonné.
