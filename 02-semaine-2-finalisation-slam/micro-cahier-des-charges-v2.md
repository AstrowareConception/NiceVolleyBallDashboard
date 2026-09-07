# Micro-cahier des charges V2 — phase 2 SLAM

## Objectif

Transformer le MVP validé en juin en une version applicative terminée, testée, documentée et livrable sous forme d'image Docker.

La phase 2 ne vise pas le déploiement final sur VPS. Ce déploiement est reporté à la phase 3 avec les étudiants SISR.

## Fonctionnalités prioritaires

### F1 — Synchronisation Weezevent stabilisée

Critères : synchronisation lancée depuis l'interface, données utiles récupérées, erreurs affichées proprement, bilan produit, traitement testé.

### F2 — Export des contacts d'un événement

Critères : événement sélectionnable, contacts affichés, export généré, champs attendus présents, test sur jeu de données vérifiable.

### F3 — Nouveaux spectateurs

Critères : règle documentée, résultat affiché, test de calcul, liste exploitable pour Brevo si consentement compatible.

### F4 — Invitations et tickets offerts

Critères : règle d'identification documentée, invitations visibles par événement, présence/absence indiquée si disponible, limites explicitées.

### F5 — Consentement et désinscription

Critères : statut stocké, statut exploitable, contact désinscrit exclu, comportement testé, règle expliquée dans la documentation client.

### F6 — Image Docker livrable

Critères : image construite, application démarrable, variables documentées, ports documentés, image publiée ou exportée, procédure fournie aux SISR.

## Hors périmètre phase 2

- déploiement final sur VPS ;
- HTTPS final ;
- reverse proxy final ;
- sauvegardes automatisées en production ;
- supervision ;
- administration système avancée ;
- refonte complète du site public ;
- application mobile ;
- connexion FFVB réelle.
