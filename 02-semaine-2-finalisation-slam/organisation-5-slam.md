# Organisation des 5 étudiants SLAM

Avec seulement 5 étudiants, il ne faut plus créer cinq pôles indépendants. L'organisation doit être légère, avec des rôles clairs et des binômes temporaires.

## Rôles recommandés

### 1. Référent projet / product owner étudiant

- maintient le micro-cahier des charges ;
- tient le backlog ;
- vérifie les priorités MoSCoW ;
- prépare les points quotidiens ;
- centralise les décisions et la démonstration.

### 2. Référent backend / API

- routes PHP/Slim ;
- intégration Weezevent ;
- intégration Brevo ;
- logique métier ;
- gestion des erreurs ;
- documentation des endpoints.

### 3. Référent données / RGPD

- évolution MCD/MLD ;
- migrations SQL ;
- règles de gestion ;
- consentement ;
- désinscription ;
- durée de conservation ;
- cohérence des données.

### 4. Référent frontend / UX

- écrans HTML/Tailwind ;
- JavaScript vanilla ;
- appels `fetch()` ;
- affichage des résultats ;
- parcours utilisateur ;
- messages d'erreur et de succès.

### 5. Référent tests / Docker / documentation

- scénarios end-to-end ;
- jeux de données ;
- recette applicative ;
- Dockerfile et image livrable ;
- documentation utilisateur ;
- documentation de passation SISR.

## Fonctionnement par binômes

Les binômes changent selon les besoins : backend + données, frontend + backend, tests + documentation, Docker + passation. Chaque fonctionnalité importante doit avoir un responsable, un critère d'acceptation, un test et une preuve.
