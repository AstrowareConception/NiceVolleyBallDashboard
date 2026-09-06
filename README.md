# NiceVolleyBallDashboard — repository documentaire

Repository documentaire du projet **Nice Volley Ball Dashboard**, réalisé avec des étudiants de BTS SIO dans le cadre de deux semaines intensives.

Ce dépôt ne contient pas l'application elle-même. Il sert à centraliser le cadrage, les consignes pédagogiques, les aides techniques, l'organisation des semaines intensives, les livrables attendus, les éléments de suivi projet et les traces utiles pour la valorisation BTS SIO.

## Navigation rapide

### 1. Comprendre le projet

- [Présentation générale du projet](00-projet/README.md)
- [Contexte client et besoin métier](00-projet/contexte-client.md)
- [Architecture et pile technique cible](00-projet/architecture-cible.md)
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

## État du projet

### Phase 1 — juin

La première semaine intensive a permis de produire un **MVP local dockerisé**. Le dashboard permet déjà de récupérer des événements Weezevent, d'extraire et traiter des données, d'identifier par exemple les nouveaux spectateurs venus au dernier événement, puis d'envoyer une liste vers Brevo.

Le MVP a été présenté au club et validé dans son état de prototype.

### Phase 2 — septembre

La deuxième semaine intensive vise la **livraison au client**. Les priorités sont la stabilisation, la prise en compte des retours client, la finalisation fonctionnelle, le déploiement sur VPS, les tests end-to-end, la documentation et la recette.

## Organisation documentaire

Le dépôt est volontairement découpé par usage :

```text
00-projet/                  Contexte global, besoin, architecture, glossaire
01-semaine-1-mvp/            Organisation et bilan de la première semaine
02-semaine-2-livraison/      Organisation de la deuxième semaine et livraison
03-aides-techniques/         Supports techniques pour les étudiants
04-suivi-projet/             Modèles WBS, MoSCoW, RACI, backlog, comptes rendus
05-evaluation-bts/           Repères pour portfolio, tableau de synthèse et preuves
```

## Principes à conserver

- Le projet doit rester centré sur un besoin réel du club.
- Les développements doivent privilégier un MVP stable plutôt qu'un périmètre trop large.
- Les données personnelles doivent être manipulées avec prudence.
- Les clés Weezevent et Brevo ne doivent jamais être versionnées.
- Les étudiants doivent distinguer clairement contribution collective et contribution individuelle.
- Les livrables doivent être exploitables par le client et réutilisables dans le cadre BTS SIO.

## Pile cible rappelée

- Frontend : HTML, Tailwind CSS, JavaScript vanilla.
- Backend : PHP avec Slim.
- Base de données : PostgreSQL.
- Infrastructure : Docker Compose, puis VPS avec reverse proxy et HTTPS.
- Intégrations : Weezevent et Brevo.

## À utiliser en début de séance

Pour reprendre le projet rapidement :

1. Lire [Contexte client et besoin métier](00-projet/contexte-client.md).
2. Relire le [Bilan du MVP livré en juin](01-semaine-1-mvp/bilan-mvp-juin.md).
3. Ouvrir les [Retours client à intégrer](02-semaine-2-livraison/retours-client-alexandre.md).
4. Compléter le [Micro-cahier des charges V2](02-semaine-2-livraison/micro-cahier-des-charges-v2.md).
5. Affecter les étudiants avec [Organisation par pôles](02-semaine-2-livraison/organisation-poles.md).
