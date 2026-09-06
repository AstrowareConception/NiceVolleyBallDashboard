# NiceVolleyBallDashboard — repository documentaire

Repository documentaire du projet **Nice Volley Ball Dashboard**, réalisé avec des étudiants de BTS SIO dans le cadre de deux semaines intensives.

Ce dépôt ne contient pas l'application elle-même. Il centralise le cadrage, les consignes pédagogiques, les aides techniques, les flux métier, l'organisation des semaines intensives, les livrables attendus, les éléments de suivi projet, les documents de livraison et les traces utiles pour la valorisation BTS SIO.

## Navigation rapide

### 1. Comprendre le projet

- [Présentation générale du projet](00-projet/README.md)
- [Contexte client et besoin métier](00-projet/contexte-client.md)
- [Architecture et pile technique cible](00-projet/architecture-cible.md)
- [Flux métier et traitements](00-projet/flux-metier.md)
- [Diagrammes Mermaid](00-projet/diagrammes.md)
- [Glossaire projet](00-projet/glossaire.md)

### 2. Semaine intensive 1 — cadrage et MVP

- [Accueil semaine 1](01-semaine-1-mvp/README.md)
- [Canevas complet de la semaine 1](01-semaine-1-mvp/canevas-semaine-1.md)
- [Bilan du MVP livré en juin](01-semaine-1-mvp/bilan-mvp-juin.md)

### 3. Semaine intensive 2 — finalisation et livraison

- [Accueil semaine 2](02-semaine-2-livraison/README.md)
- [Canevas complet de la semaine 2](02-semaine-2-livraison/canevas-semaine-2.md)
- [Retours client à intégrer](02-semaine-2-livraison/retours-client-alexandre.md)
- [Micro-cahier des charges V2](02-semaine-2-livraison/micro-cahier-des-charges-v2.md)
- [Matrice de traçabilité](02-semaine-2-livraison/matrice-tracabilite.md)
- [RGPD opérationnel](02-semaine-2-livraison/rgpd-operationnel.md)
- [Organisation par pôles](02-semaine-2-livraison/organisation-poles.md)
- [Recette, tests et livraison](02-semaine-2-livraison/recette-tests-livraison.md)

### 4. Aides techniques

- [Accueil des aides techniques](03-aides-techniques/README.md)
- [Aide Slim / API PHP](03-aides-techniques/aide-slim-api-php.md)
- [Aide JavaScript vanilla + fetch](03-aides-techniques/aide-javascript-vanilla-fetch.md)
- [Aide MERISE / données](03-aides-techniques/aide-merise-donnees.md)

### 5. Suivi projet

- [Accueil suivi projet](04-suivi-projet/README.md)
- [Modèle de backlog](04-suivi-projet/modeles/backlog.md)
- [Modèle MoSCoW](04-suivi-projet/modeles/moscow.md)
- [Modèle RACI](04-suivi-projet/modeles/raci.md)
- [Modèle de compte rendu quotidien](04-suivi-projet/modeles/compte-rendu-quotidien.md)

### 6. Valorisation BTS SIO

- [Accueil évaluation BTS](05-evaluation-bts/README.md)

### 7. Livraison client

- [Accueil livraison](06-livraison/README.md)
- [Checklist de livraison](06-livraison/checklist-livraison.md)
- [Procédure de déploiement](06-livraison/procedure-deploiement.md)
- [Procédure sauvegarde/restauration](06-livraison/procedure-sauvegarde-restauration.md)
- [Procédure de remise client](06-livraison/procedure-remise-client.md)
- [PV de recette](06-livraison/pv-recette.md)

## État du projet

### Phase 1 — juin

La première semaine intensive a permis de produire un **MVP local dockerisé**. Le dashboard permet déjà de récupérer des événements Weezevent, d'extraire et traiter des données, d'identifier par exemple les nouveaux spectateurs venus au dernier événement, puis d'envoyer une liste vers Brevo.

Le MVP a été présenté au club et validé dans son état de prototype.

### Phase 2 — septembre

La deuxième semaine intensive vise la **livraison au client**. Les priorités sont la stabilisation, la prise en compte des retours client, la finalisation fonctionnelle, le déploiement sur VPS, les tests end-to-end, la documentation, la recette et le passage de relais.

## Organisation documentaire

```text
00-projet/                  Contexte global, besoin, architecture, flux, diagrammes, glossaire
01-semaine-1-mvp/            Organisation et bilan de la première semaine
02-semaine-2-livraison/      Organisation de la deuxième semaine, cahier des charges V2, traçabilité
03-aides-techniques/         Supports techniques pour les étudiants
04-suivi-projet/             Modèles WBS, MoSCoW, RACI, backlog, comptes rendus
05-evaluation-bts/           Repères pour portfolio, tableau de synthèse et preuves
06-livraison/                Documents de déploiement, recette, exploitation et remise client
.github/ISSUE_TEMPLATE/      Modèles d'issues pour piloter la semaine
```

## À utiliser en début de semaine 2

1. Lire [Contexte client et besoin métier](00-projet/contexte-client.md).
2. Relire le [Bilan du MVP livré en juin](01-semaine-1-mvp/bilan-mvp-juin.md).
3. Ouvrir les [Retours client à intégrer](02-semaine-2-livraison/retours-client-alexandre.md).
4. Compléter le [Micro-cahier des charges V2](02-semaine-2-livraison/micro-cahier-des-charges-v2.md).
5. Transformer les besoins en tickets avec la [Matrice de traçabilité](02-semaine-2-livraison/matrice-tracabilite.md).
6. Affecter les étudiants avec [Organisation par pôles](02-semaine-2-livraison/organisation-poles.md).
7. Préparer la livraison avec [06-livraison](06-livraison/README.md).

## Principes à conserver

- Le projet doit rester centré sur un besoin réel du club.
- Les développements doivent privilégier une version stable plutôt qu'un périmètre trop large.
- Les données personnelles doivent être manipulées avec prudence.
- Les clés Weezevent et Brevo ne doivent jamais être versionnées.
- Les étudiants doivent distinguer clairement contribution collective et contribution individuelle.
- Les livrables doivent être exploitables par le client et réutilisables dans le cadre BTS SIO.
