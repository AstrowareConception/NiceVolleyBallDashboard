# NiceVolleyBallDashboard — repository documentaire

Repository documentaire du projet **Nice Volley Ball Dashboard**, réalisé avec des étudiants de BTS SIO.

Ce dépôt ne contient pas l'application elle-même. Il centralise le cadrage, les consignes pédagogiques, les aides techniques, les documents de suivi, la préparation de la livraison et les traces utiles pour la valorisation BTS SIO.

## Organisation actualisée

La phase 2 change de périmètre : elle est désormais menée uniquement avec **5 étudiants SLAM**. Le déploiement final n'est plus attendu en fin de semaine. La semaine SLAM doit terminer le code, stabiliser l'application, produire une image Docker livrable et préparer une passation exploitable par les SISR.

```text
Phase 1 — Juin       : cadrage et MVP local dockerisé
Phase 2 — Septembre  : finalisation applicative SLAM + image Docker
Phase 3 — Ensuite    : déploiement VPS, sauvegardes et automatisations SISR
```

## Navigation rapide

### 1. Comprendre le projet

- [Présentation générale](00-projet/README.md)
- [Contexte client](00-projet/contexte-client.md)
- [Architecture cible](00-projet/architecture-cible.md)
- [Flux métier](00-projet/flux-metier.md)
- [Diagrammes Mermaid](00-projet/diagrammes.md)
- [Glossaire](00-projet/glossaire.md)

### 2. Phase 1 — MVP de juin

- [Accueil phase 1](01-semaine-1-mvp/README.md)
- [Canevas complet de la semaine 1](01-semaine-1-mvp/canevas-semaine-1.md)
- [Bilan du MVP livré en juin](01-semaine-1-mvp/bilan-mvp-juin.md)

### 3. Phase 2 — finalisation SLAM

- [Accueil phase 2 SLAM](02-semaine-2-finalisation-slam/README.md)
- [Organisation des 5 SLAM](02-semaine-2-finalisation-slam/organisation-5-slam.md)
- [Planning jour par jour](02-semaine-2-finalisation-slam/planning-jour-par-jour.md)
- [Micro-cahier des charges V2](02-semaine-2-finalisation-slam/micro-cahier-des-charges-v2.md)
- [Matrice de traçabilité](02-semaine-2-finalisation-slam/matrice-tracabilite.md)
- [Tests et recette applicative](02-semaine-2-finalisation-slam/tests-recette-applicative.md)
- [Image Docker livrable](02-semaine-2-finalisation-slam/image-docker-livrable.md)
- [Documentation utilisateur client](02-semaine-2-finalisation-slam/documentation-utilisateur-client.md)
- [Passation technique vers les SISR](02-semaine-2-finalisation-slam/passation-sisr.md)
- [RGPD opérationnel](02-semaine-2-finalisation-slam/rgpd-operationnel.md)

### 4. Phase 3 — déploiement SISR

- [Accueil phase 3 SISR](03-phase-3-deploiement-sisr/README.md)
- [Prérequis VPS et SSH](03-phase-3-deploiement-sisr/prerequis-vps-ssh.md)
- [Déploiement de l'image Docker](03-phase-3-deploiement-sisr/deploiement-image-docker.md)
- [Sauvegardes et automatisations](03-phase-3-deploiement-sisr/sauvegardes-automatisations.md)
- [Recette technique de déploiement](03-phase-3-deploiement-sisr/recette-technique-deploiement.md)

### 5. Aides techniques

- [Aides techniques](04-aides-techniques/README.md)
- [Slim / API PHP](04-aides-techniques/aide-slim-api-php.md)
- [JavaScript vanilla + fetch](04-aides-techniques/aide-javascript-vanilla-fetch.md)
- [MERISE / données](04-aides-techniques/aide-merise-donnees.md)

### 6. Suivi et évaluation

- [Suivi projet](05-suivi-projet/README.md)
- [Valorisation BTS SIO](06-evaluation-bts/README.md)

## Principes non négociables

- ne pas repartir de zéro sans raison ;
- terminer, tester et documenter le code ;
- ne jamais versionner de clé Weezevent ou Brevo ;
- masquer les données personnelles dans les captures ;
- produire une image Docker reproductible ;
- fournir aux SISR une passation technique claire.
