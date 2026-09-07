# Flux métier du dashboard

## Flux 1 — Synchroniser Weezevent

1. l'utilisateur lance la synchronisation ;
2. le backend appelle Weezevent ;
3. les événements, billets, contacts et invitations disponibles sont récupérés ;
4. les données sont normalisées ;
5. les doublons sont traités ;
6. la base est mise à jour ;
7. un bilan est affiché et journalisé.

## Flux 2 — Exporter les contacts d'un événement

1. l'utilisateur sélectionne un événement ;
2. le dashboard affiche les contacts associés ;
3. l'utilisateur déclenche l'export ;
4. le fichier exporté contient uniquement les champs nécessaires ;
5. le résultat est vérifié sur un jeu de données connu.

## Flux 3 — Identifier les nouveaux spectateurs

Un nouveau spectateur est, dans la règle de départ, un contact dont la première participation connue correspond à l'événement sélectionné. La règle doit être documentée et testée.

## Flux 4 — Gérer invitations et tickets offerts

Le système doit distinguer les tickets classiques des invitations lorsque les données Weezevent le permettent. La présence réelle doit être affichée uniquement si l'information existe dans les données accessibles.

## Flux 5 — Gérer consentement et désinscription

Un contact désinscrit ou non éligible ne doit pas être envoyé vers Brevo. Le bilan d'envoi doit indiquer les contacts envoyés, ignorés et en erreur.

## Flux 6 — Préparer la passation SISR

La phase 2 doit fournir : image Docker, tag, variables d'environnement, ports, volumes, migrations, commandes de santé, limites connues et procédure de lancement.
