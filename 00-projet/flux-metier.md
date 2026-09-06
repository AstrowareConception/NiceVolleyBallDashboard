# Flux métier et traitements principaux

Ce document décrit les flux à stabiliser pour la semaine 2. Il sert de passerelle entre les besoins client, le modèle de données, les routes API, les écrans et les tests.

## Flux 1 — Synchronisation Weezevent

Objectif : récupérer les données utiles de billetterie depuis Weezevent et les intégrer dans le dashboard.

Déclencheur : action manuelle d'un gestionnaire ou tâche automatisée future.

Entrées attendues : événements, commandes, billets, invitations, statut de présence si disponible, informations de contact.

Traitements :

- appeler l'API Weezevent ;
- transformer les données externes en format interne ;
- créer ou mettre à jour les événements ;
- créer ou mettre à jour les contacts ;
- associer billets, commandes ou participations ;
- tracer la synchronisation ;
- signaler les erreurs sans bloquer tout le traitement si possible.

Sortie attendue : un bilan de synchronisation indiquant au minimum le nombre d'éléments récupérés, créés, mis à jour, ignorés et en erreur.

## Flux 2 — Contacts d'un événement

Objectif : permettre au club d'obtenir tous les contacts associés à un match ou événement.

Règle fonctionnelle : un contact est associé à un événement s'il possède au moins un billet, une commande, une invitation ou une présence rattachée à cet événement.

Sorties possibles : affichage écran, export CSV, liste exploitable pour Brevo.

Points à valider : champs exportés, ordre des colonnes, inclusion ou exclusion des contacts sans email, traitement des doublons.

## Flux 3 — Nouveaux spectateurs

Objectif : identifier les personnes venues pour la première fois lors d'un événement donné.

Règle de départ : un nouveau spectateur est un contact présent ou inscrit à l'événement sélectionné et qui ne possède pas d'achat ou présence antérieure dans l'historique connu.

Limite : le résultat dépend de l'historique réellement récupéré depuis Weezevent. Si l'historique est incomplet, le dashboard doit l'indiquer.

## Flux 4 — Invitations et tickets offerts

Objectif : distinguer les billets payants des invitations ou tickets offerts.

Données nécessaires : type de billet, tarif, code invitation, canal d'émission, statut de scan ou présence si disponible.

Règles à stabiliser :

- une invitation doit être identifiable dans les données Weezevent ;
- une invitation utilisée correspond à une invitation rattachée à une présence ou un scan ;
- une invitation non utilisée correspond à une invitation émise sans présence confirmée ;
- si le statut de présence n'est pas disponible, le système doit afficher une limite explicite.

## Flux 5 — Consentement et désinscription

Objectif : éviter d'envoyer vers Brevo des contacts qui ne doivent pas recevoir de communications.

Statuts minimaux recommandés : consentement inconnu, consentement accepté, désinscrit, opposition.

Règle minimale : un contact désinscrit ou en opposition ne doit pas être synchronisé vers une liste marketing Brevo.

Points à tracer : date de désinscription, source du consentement, dernière synchronisation Brevo, raison d'exclusion éventuelle.

## Flux 6 — Synchronisation ou export Brevo

Objectif : envoyer vers Brevo une liste de contacts éligibles issue d'un segment ou d'un événement.

Traitements :

- sélectionner le segment ou l'événement ;
- filtrer les contacts sans consentement valide selon la règle retenue ;
- exclure les désinscrits ;
- préparer la liste ;
- appeler l'API Brevo ou générer un export compatible ;
- enregistrer le bilan.

Sortie attendue : nombre de contacts envoyés, ignorés, déjà présents, en erreur.

## Flux 7 — Livraison et exploitation

Objectif : rendre le service utilisable par le client.

Éléments attendus : environnement cible, URL, HTTPS, variables d'environnement, sauvegarde, restauration, procédure de mise à jour, documentation utilisateur, documentation d'exploitation, PV de recette.

## Principe de validation

Chaque flux doit être relié à au moins :

- une fonctionnalité ;
- un écran ou une route API ;
- une donnée ou table concernée ;
- un test de recette ;
- une preuve livrable.

Voir aussi : [Matrice de traçabilité](../02-semaine-2-livraison/matrice-tracabilite.md).
