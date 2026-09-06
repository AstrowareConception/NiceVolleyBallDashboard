# Recette, tests et livraison

## Objectif

La recette doit prouver que la version livrée répond aux besoins prioritaires du client et qu'elle peut être exploitée dans des conditions acceptables.

## Scénario de démonstration prioritaire

1. Accéder à l'application déployée ou à la préproduction.
2. Se connecter si l'authentification est en place.
3. Synchroniser les données Weezevent.
4. Consulter la liste des événements.
5. Sélectionner un événement.
6. Exporter tous les contacts associés à cet événement.
7. Afficher les nouveaux spectateurs de cet événement.
8. Identifier les invitations ou tickets offerts si les données le permettent.
9. Vérifier le consentement et la désinscription.
10. Envoyer ou préparer une liste Brevo en excluant les contacts non autorisés.
11. Consulter les logs de synchronisation.
12. Déclencher ou montrer la sauvegarde.
13. Présenter la documentation utilisateur et exploitation.

## Tests end-to-end attendus

| ID | Parcours | Résultat attendu | Statut |
|---|---|---|---|
| E2E-01 | Synchronisation Weezevent | Les événements et contacts sont récupérés | À compléter |
| E2E-02 | Export contacts événement | Un fichier exploitable est généré | À compléter |
| E2E-03 | Nouveaux spectateurs | La liste correspond au jeu d'essai | À compléter |
| E2E-04 | Invitations | Les tickets offerts sont identifiés si disponibles | À compléter |
| E2E-05 | Consentement | Un contact désinscrit est exclu | À compléter |
| E2E-06 | Envoi Brevo | La liste est créée ou synchronisée | À compléter |
| E2E-07 | Erreur API externe | L'erreur est affichée et loggée | À compléter |
| E2E-08 | Redémarrage service | L'application reste fonctionnelle | À compléter |
| E2E-09 | Sauvegarde | Une sauvegarde est produite | À compléter |
| E2E-10 | Restauration | Une restauration est testée ou documentée | À compléter |

## Critères de livraison

La livraison est acceptable si :

- les fonctionnalités prioritaires sont démontrables ;
- le client comprend comment utiliser l'outil ;
- le déploiement est documenté ;
- les secrets ne sont pas exposés ;
- les données sont sauvegardables ;
- les tests principaux sont exécutés ;
- les limites sont clairement indiquées ;
- le backlog restant est propre.

## Documents à remettre

- guide utilisateur ;
- documentation technique ;
- documentation d'exploitation ;
- plan de test ;
- rapport de recette ;
- backlog restant ;
- bilan projet ;
- liste des accès à remettre au client.

## Points bloquants à surveiller

- VPS non disponible ;
- accès client incomplets ;
- clés API manquantes ;
- données Weezevent insuffisantes pour distinguer invitations ou présence ;
- consentement non exploitable ;
- pipeline de déploiement instable ;
- documentation non finalisée ;
- absence de test de restauration.
