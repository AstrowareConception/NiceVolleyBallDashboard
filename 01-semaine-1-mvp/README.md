# Semaine 1 — Cadrage et MVP

## Objectif de la semaine

La première semaine intensive avait pour objectif de produire un premier prototype fonctionnel du dashboard Nice Volley Ball.

Le travail devait permettre aux étudiants de découvrir le contexte client, de répartir les rôles, de produire un cadrage projet, de concevoir une première architecture, puis de développer un MVP démontrable.

## Résultat obtenu

À l'issue de la semaine, un MVP local dockerisé a été obtenu. Il permet notamment :

- la récupération d'événements Weezevent ;
- l'extraction et le traitement de données ;
- l'identification de nouveaux spectateurs venus au dernier événement ;
- la préparation ou l'envoi d'une liste vers Brevo ;
- une démonstration client validée en l'état.

Le projet n'était pas encore déployé à cette étape. Les environnements étaient locaux et dockerisés.

## Documents de cette section

- [Canevas complet de la semaine 1](canevas-semaine-1.md)
- [Bilan du MVP livré en juin](bilan-mvp-juin.md)

## Aides techniques mobilisées

- [Aide Slim / API PHP](../03-aides-techniques/aide-slim-api-php.md)
- [Aide JavaScript vanilla + fetch](../03-aides-techniques/aide-javascript-vanilla-fetch.md)
- [Aide MERISE / données](../03-aides-techniques/aide-merise-donnees.md)

## Sous-découpage pédagogique

### 1. Cadrage

- compréhension du besoin ;
- analyse des outils existants ;
- choix du périmètre MVP ;
- répartition SLAM / SISR ;
- organisation en pôles.

### 2. Conception

- premières maquettes ;
- modèle de données ;
- architecture technique ;
- choix Slim, PostgreSQL, Docker ;
- étude des API Weezevent et Brevo.

### 3. Réalisation

- backend API ;
- frontend HTML/Tailwind/JS ;
- stockage PostgreSQL ;
- synchronisation Weezevent ;
- segmentation simple ;
- envoi ou export vers Brevo.

### 4. Restitution

- démonstration du MVP ;
- validation par le club ;
- identification des limites ;
- préparation de la semaine 2.
