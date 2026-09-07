# Tests et recette applicative — phase 2 SLAM

## Scénario principal

1. lancer l'application en local Docker ;
2. accéder au dashboard ;
3. lancer une synchronisation Weezevent ;
4. consulter le bilan ;
5. sélectionner un événement ;
6. afficher les contacts associés ;
7. exporter les contacts ;
8. identifier les nouveaux spectateurs ;
9. afficher les invitations si disponibles ;
10. vérifier consentement/désinscription ;
11. lancer une synchronisation Brevo ;
12. vérifier que les contacts non éligibles sont exclus ;
13. vérifier logs ou bilans.

## Tests minimaux

| ID | Test | Résultat attendu | Statut | Preuve |
|---|---|---|---|---|
| T01 | L'application démarre via Docker | Dashboard accessible | À faire | capture |
| T02 | Synchronisation Weezevent | Bilan affiché | À faire | capture + log |
| T03 | Export événement | Fichier généré | À faire | fichier export |
| T04 | Nouveaux spectateurs | Liste conforme | À faire | jeu + capture |
| T05 | Contact désinscrit | Contact exclu de Brevo | À faire | test |
| T06 | Synchronisation Brevo | Liste créée ou mise à jour | À faire | capture |
| T07 | Erreur API externe | Message compréhensible | À faire | capture |
| T08 | Image Docker | Lancement depuis image | À faire | commande |

Une fonctionnalité n'est terminée que si elle est développée, intégrée, testée, documentée et associée à une preuve.
