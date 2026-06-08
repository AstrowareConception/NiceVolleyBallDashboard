# Semaine intensive — Projet Nice Volley Ball Dashboard

## 1. Objectif général de la semaine

Pendant cette semaine intensive, vous allez travailler sur un projet réel proposé par le Nice Volley Ball. Le club dispose aujourd’hui de plusieurs outils séparés : Weezevent pour la billetterie, Brevo pour les newsletters et les contacts, des fichiers Excel ou Google Sheets pour certains suivis manuels, ainsi que des outils institutionnels comme l’extranet FFVB. Ces outils sont utiles, mais ils ne communiquent pas suffisamment entre eux.

Le problème principal est donc la dispersion des données. Le club ne dispose pas d’une vision claire du parcours d’un supporter : première venue, nombre de matchs vus, intérêt pour les abonnements, présence en famille, potentiel merchandising, réaction aux campagnes ou retour après un événement. Les communications restent trop générales et les relances ne sont pas assez automatisées.

L’objectif de la semaine n’est pas de développer un CRM complet. L’objectif est de produire un **prototype fonctionnel démontrable** d’un dashboard interne permettant d’importer des données de billetterie, de les stocker proprement, de les consulter, de générer quelques indicateurs simples, de segmenter les contacts et de préparer un export ou une synchronisation vers Brevo.

Le projet est majoritairement orienté SLAM, mais les étudiants SISR ont un rôle important sur l’environnement technique, le déploiement, la sécurité, les sauvegardes, les automatisations et la documentation d’exploitation.

## 2. Résultat attendu vendredi

À la fin de la semaine, la classe devra pouvoir présenter un prototype cohérent.

Le scénario de démonstration visé est le suivant :

Un gestionnaire du club accède à une interface web interne. Il importe un fichier CSV issu de Weezevent ou simulant un export Weezevent. Le système nettoie les données, crée ou met à jour des contacts dans une base PostgreSQL, détecte certains doublons simples et affiche un bilan d’import. Le gestionnaire peut ensuite consulter la liste des contacts, ouvrir une fiche contact, visualiser quelques indicateurs dans un tableau de bord, afficher des segments simples comme “nouveaux visiteurs”, “supporters réguliers” ou “abonnés potentiels”, puis générer un export CSV compatible Brevo ou déclencher un appel API Brevo sur un compte de test.

En parallèle, les étudiants SISR devront être capables de montrer comment l’application est lancée, comment les services sont organisés, comment les variables sensibles sont protégées, comment la base est sauvegardée, où sont les logs et quelles précautions sont prévues pour un futur déploiement.

## 3. Pile technique retenue

La pile technique doit rester adaptée au niveau actuel de la classe. Le projet ne doit pas reposer sur React, Vue ou Angular. L’objectif est de renforcer les bases : HTML, CSS, JavaScript, PHP, SQL, API REST, Docker et déploiement.

La pile retenue est la suivante :

Côté interface utilisateur, le projet utilisera **HTML**, **Tailwind CSS** et **JavaScript vanilla**. Les pages seront donc construites avec du HTML classique, stylisées avec Tailwind, et rendues dynamiques avec du JavaScript écrit par les étudiants. Les appels au backend se feront avec `fetch()`. Le JavaScript devra gérer l’affichage des contacts, les formulaires, les messages d’erreur, les tableaux, les cartes statistiques et les actions utilisateur.

Côté backend, le projet utilisera **PHP avec le micro-framework Slim**. Slim permettra de créer une API REST légère, structurée et compréhensible. Il servira à définir les routes, recevoir les requêtes HTTP, lire les fichiers importés, interroger la base de données, renvoyer des réponses JSON et appeler éventuellement l’API Brevo.

Côté base de données, le projet utilisera **PostgreSQL**. La base contiendra les contacts, événements, achats, imports, segments, campagnes, utilisateurs et rôles éventuels. Le modèle devra être issu d’une vraie réflexion MERISE : règles de gestion, dictionnaire de données, MCD, MLD, puis script SQL.

Côté déploiement, le projet utilisera **Docker Compose**. Les SISR devront mettre en place un environnement de développement comprenant au minimum le backend PHP/Slim, PostgreSQL, Adminer et éventuellement Nginx. Une réflexion sera également menée sur un environnement de production plus sécurisé avec reverse proxy, HTTPS, variables d’environnement, sauvegardes et logs.

Côté intégrations externes, **Brevo** est prioritaire. L’objectif est de comprendre comment créer ou mettre à jour des contacts, gérer des attributs ou préparer des listes. **Weezevent** pourra être étudié, mais l’import CSV reste la priorité, car il est plus réaliste, plus rapide à prototyper et directement utile pour exploiter un historique de billetterie.

## 4. Organisation générale des équipes

La classe sera répartie en pôles de travail. Les groupes pourront évoluer au cours de la semaine, mais chaque pôle doit avoir des responsabilités claires.

Un binôme assurera le pilotage projet. Ce binôme ne sera pas exempté de production technique ou documentaire, mais il sera garant de l’organisation : WBS, RACI, MoSCoW, Gantt, PERT simplifié, backlog, suivi des tâches, arbitrages et préparation de la démonstration finale. Idéalement, ce binôme sera composé d’un étudiant SLAM et d’un étudiant SISR.

Un pôle UX / UI travaillera sur la charte graphique, les parcours utilisateurs et les maquettes. Ce pôle devra produire des écrans réalistes, puis aider à leur intégration en HTML / Tailwind. Ce pôle peut accueillir des étudiants SISR, car le maquettage, l’ergonomie et la documentation visuelle sont des tâches transversales.

Un pôle MERISE / données / RGPD travaillera sur les règles de gestion, le dictionnaire de données, le MCD, le MLD, le script SQL et les données de test. Ce pôle est prioritaire en début de semaine, car le backend dépend directement de la structure de la base.

Un pôle backend PHP / Slim travaillera sur l’API REST. Il devra gérer la connexion à PostgreSQL, les routes, l’import CSV, la création et la consultation des contacts, les statistiques, les segments et l’export Brevo.

Un pôle frontend HTML / Tailwind / JavaScript travaillera sur les pages visibles du dashboard. Il devra produire des interfaces simples, propres et fonctionnelles, puis les connecter à l’API avec `fetch()`.

Un pôle SISR / déploiement / sécurité / automatisation travaillera sur Docker, l’environnement technique, les sauvegardes, les logs, les variables d’environnement, la sécurité minimale et la documentation d’exploitation.

## 5. Méthode projet imposée

Le projet doit permettre de mettre en pratique les outils de gestion de projet vus en cours. Ces outils ne doivent pas rester théoriques : ils doivent aider la classe à organiser réellement le travail.

Le binôme de pilotage devra produire un WBS pour découper le projet en lots et sous-tâches. Il devra aussi produire un MoSCoW pour distinguer les fonctionnalités indispensables, importantes, optionnelles et hors périmètre. Un RACI devra permettre de savoir qui est responsable, qui contribue, qui est consulté et qui doit être informé. Un Gantt simplifié devra présenter la planification de la semaine. Un PERT simplifié devra faire apparaître les dépendances critiques, notamment entre MERISE, base de données, API, front et déploiement.

Le backlog devra être tenu à jour chaque jour. Les tâches devront être rédigées clairement, avec un responsable, une priorité et un critère d’acceptation. Une tâche comme “faire le backend” est trop vague. Une tâche correcte serait plutôt : “Créer la route GET /contacts qui renvoie la liste des contacts au format JSON avec id, nom, prénom, email et segment principal.”

## 6. MoSCoW du projet

Les fonctionnalités indispensables pour vendredi sont les suivantes : disposer d’un modèle de données cohérent, d’une base PostgreSQL fonctionnelle, d’un import CSV Weezevent simulé, d’une API Slim capable de lire et écrire les contacts, d’une interface HTML / Tailwind affichant les contacts, d’une fiche contact, d’un dashboard avec quelques statistiques, d’une segmentation simple, d’un export CSV compatible Brevo ou d’un appel API Brevo de test, d’un environnement Docker de développement et d’une documentation minimale.

Les fonctionnalités importantes mais non bloquantes sont les suivantes : authentification simple, gestion de deux rôles, détection basique des doublons, logs d’import, sauvegarde automatique de la base, interface plus soignée, messages d’erreur clairs, historique des imports et synchronisation Brevo par API.

Les fonctionnalités optionnelles sont les suivantes : connexion API Weezevent, environnement de production complet avec Nginx et HTTPS, graphiques avancés, tests automatisés, CI/CD, historique détaillé des campagnes et restauration automatisée documentée.

Les éléments hors périmètre pour cette semaine sont les suivants : CRM complet, application mobile, espace supporter public, paiement intégré, connexion FFVB réelle, programme de fidélité complet, refonte du site du club et automatisations marketing avancées en production.

## 7. Planning détaillé de la semaine

### Lundi matin — Cadrage, méthode projet et MVP

La première demi-journée sert à poser le cadre. La classe reprend le contexte du Nice Volley Ball, les travaux déjà réalisés et l’objectif final du prototype. Le binôme de pilotage anime la clarification du MVP. Il formalise les premières versions du MoSCoW, du WBS et du backlog.

Le pôle MERISE démarre immédiatement. Il doit identifier les principales entités : contact, événement, achat, import, segment, campagne, utilisateur, rôle et consentement éventuel. Il commence les règles de gestion et prépare une première version du MCD.

Le pôle technique confirme la pile retenue : HTML, Tailwind, JavaScript vanilla, PHP Slim, PostgreSQL, Docker Compose, Brevo API et import CSV Weezevent.

Livrables attendus lundi midi : MVP validé, groupes constitués, MoSCoW V1, WBS V1, premières entités identifiées, backlog initial.

### Lundi après-midi — Squelettes techniques et premières conceptions

Le pôle MERISE finalise un MCD V1 et commence le MLD. Le pôle backend crée le projet Slim, prépare l’arborescence, configure Composer, met en place une première route de test et prépare la connexion à PostgreSQL. Le pôle frontend prépare les premières pages HTML, installe ou configure Tailwind, crée une structure de fichiers claire et commence les écrans statiques.

Le pôle SISR prépare un premier docker-compose de développement avec PostgreSQL, Adminer et le backend PHP. Il documente les commandes de lancement. Le pôle UX produit les premières maquettes du dashboard, de la liste contacts, de la fiche contact et de l’écran d’import.

Livrables attendus lundi soir : dépôt Git initial, docker-compose DEV initial, projet Slim lancé, première page HTML/Tailwind, MCD V1, MLD V1, maquettes V1.

### Mardi matin — Base de données, CSV de test et premières routes API

Le mardi matin est consacré au socle de données. Le pôle MERISE produit le script SQL de création de base et un jeu de données fictif. Il prépare également un CSV Weezevent simulé avec des colonnes réalistes : nom, prénom, email, téléphone, événement, date d’achat, tarif, quantité, montant, code promotionnel éventuel.

Le backend développe les premières routes : liste des contacts, détail d’un contact, création d’un contact de test et route de santé de l’API. Le frontend commence à appeler l’API avec `fetch()` pour afficher une réponse simple dans une page HTML.

Le SISR stabilise Docker : variables d’environnement, volume PostgreSQL, accès Adminer en développement, documentation d’installation.

Livrables attendus mardi midi : script SQL fonctionnel, base PostgreSQL créée, CSV de test, premières routes API, premier appel `fetch()` depuis une page HTML.

### Mardi après-midi — Import CSV et affichage dynamique

Le mardi après-midi, le backend développe l’import CSV. L’objectif est de lire un fichier, contrôler quelques champs, insérer les contacts, rattacher les achats aux événements et produire un bilan d’import. La détection des doublons peut commencer simplement par l’adresse email.

Le frontend développe l’écran d’import : formulaire de sélection de fichier, bouton d’envoi, appel `fetch()` vers l’API, affichage du résultat de l’import. Il développe aussi la liste des contacts sous forme de tableau dynamique alimenté par l’API.

Le pôle UX ajuste les maquettes selon ce qui est réellement faisable. Le binôme projet met à jour le RACI et le Gantt.

Livrables attendus mardi soir : import CSV partiellement fonctionnel, tableau contacts alimenté dynamiquement, bilan d’import affiché, RACI V1, Gantt V1.

### Mercredi matin — Fiche contact, dashboard et segmentation

Le mercredi matin doit produire de la valeur métier. Le backend ajoute les routes permettant de récupérer une fiche contact détaillée, les statistiques du dashboard et les premiers segments. Les règles de segmentation doivent rester simples : nouveau visiteur, supporter régulier, abonné potentiel, contact inactif.

Le frontend développe la fiche contact et le tableau de bord. Les statistiques peuvent être affichées sous forme de cartes simples : nombre total de contacts, nouveaux contacts, visiteurs récurrents, abonnés potentiels, nombre d’achats importés. Les graphiques ne sont pas obligatoires ; des cartes et tableaux bien faits suffisent.

Le pôle MERISE vérifie que les routes et les données respectent le modèle. Le SISR commence les scripts de sauvegarde PostgreSQL.

Livrables attendus mercredi midi : fiche contact, route statistiques, route segments, dashboard simple, script de sauvegarde initial.

### Mercredi après-midi — Export Brevo et API Brevo

Le mercredi après-midi est consacré à Brevo. Le backend doit au minimum générer un export CSV compatible Brevo à partir d’un segment. Si possible, il doit aussi réussir un appel API Brevo sur un compte de test : création d’un contact fictif, mise à jour d’un attribut ou ajout à une liste.

Le frontend développe l’écran de segmentation et d’export : choix d’un segment, prévisualisation des contacts concernés, bouton d’export CSV ou bouton de synchronisation de test. Le SISR documente la gestion des clés API : fichier `.env`, variables d’environnement, exclusion du dépôt Git, règles de sécurité.

Livrables attendus mercredi soir : export CSV Brevo ou appel API Brevo de test, écran segments/export, documentation des variables sensibles, logs d’import ou d’export.

### Jeudi matin — Authentification, rôles et sécurité

Le jeudi matin sert à sécuriser le prototype. Le backend met en place une authentification simple si elle n’existe pas encore. L’objectif n’est pas de développer un système complexe, mais de comprendre les principes : mot de passe hashé, route de connexion, session ou token simple, protection minimale des routes sensibles.

Si le temps le permet, deux rôles sont prévus : administrateur et utilisateur restreint. L’administrateur peut importer, exporter et consulter les données. L’utilisateur restreint peut consulter le dashboard et les contacts, mais ne peut pas supprimer ou modifier les données sensibles.

Le frontend ajoute l’écran de connexion et adapte l’affichage selon le rôle si cette partie est prête. Le SISR vérifie que la base n’est pas exposée publiquement, qu’Adminer est réservé au développement, que les secrets ne sont pas versionnés et que les sauvegardes ne sont pas accessibles depuis le web.

Livrables attendus jeudi midi : connexion simple, protection minimale des routes, checklist sécurité, séparation claire entre données de test et données sensibles.

### Jeudi après-midi — Intégration, tests et documentation

À partir du jeudi après-midi, les grosses nouvelles fonctionnalités doivent être limitées. La priorité devient l’intégration. Les groupes doivent corriger les bugs, tester les parcours, stabiliser les écrans, documenter les routes API, documenter l’installation et préparer la démonstration.

Le binôme projet organise une recette interne. Le scénario testé doit être complet : lancer l’environnement, créer la base, importer un CSV, afficher les contacts, ouvrir une fiche contact, consulter le dashboard, générer un segment, exporter vers Brevo ou générer le CSV, vérifier les logs et lancer une sauvegarde.

Livrables attendus jeudi soir : version intégrée, documentation technique, documentation utilisateur courte, scénario de recette, liste des fonctionnalités réellement disponibles.

### Vendredi matin — Préparation de la démonstration finale

Le vendredi matin est consacré à la préparation de la restitution. La démonstration doit être unifiée. Il ne faut pas présenter cinq travaux indépendants, mais un projet global.

Le binôme projet prépare l’introduction : contexte, besoin, MVP, organisation, méthode de travail. Le pôle UX présente la logique des écrans. Le pôle MERISE présente le modèle de données. Le pôle backend présente l’API Slim, l’import CSV et l’intégration Brevo. Le pôle frontend présente l’interface HTML / Tailwind / JavaScript. Le pôle SISR présente Docker, le déploiement, la sécurité, les logs et les sauvegardes.

Livrables attendus vendredi midi : support de présentation, démonstration répétée, rôles de présentation répartis, application prête à être montrée.

### Vendredi après-midi — Démonstration, bilan et perspectives

Le vendredi après-midi est consacré à la démonstration finale, puis au bilan. Chaque étudiant doit être capable d’expliquer sa contribution personnelle. La restitution doit montrer ce qui fonctionne réellement, ce qui reste à faire, les difficultés rencontrées et les choix effectués.

Le bilan devra être honnête. Il devra distinguer les fonctionnalités terminées, les fonctionnalités partiellement terminées, les limites techniques, les risques, les évolutions possibles et les tâches à poursuivre après la semaine intensive.

Livrables attendus vendredi soir : prototype démontrable, documentation, MCD/MLD, script SQL, schéma d’infrastructure, backlog restant, bilan projet et contributions individuelles.

## 8. Missions détaillées par pôle

### Pôle Pilotage projet

Le pôle pilotage est responsable de l’organisation globale. Il tient à jour le WBS, le MoSCoW, le RACI, le Gantt, le PERT simplifié et le backlog. Il anime les points d’avancement, identifie les blocages, alerte sur les retards et prépare la démonstration finale.

Il doit veiller à ce que les groupes ne travaillent pas en silo. Le modèle de données doit correspondre aux besoins de l’API. L’API doit fournir les données nécessaires au front. Le front doit respecter les maquettes. Le déploiement doit être compatible avec les choix techniques.

### Pôle UX / UI / maquettes

Le pôle UX / UI produit une charte graphique courte et des maquettes exploitables. Il travaille sur l’ergonomie du dashboard, le parcours d’import, la consultation d’un contact, l’affichage des statistiques et la préparation d’un export.

Il doit aussi aider à l’intégration HTML / Tailwind. Ses productions doivent donc être réalistes : composants simples, boutons clairs, tableaux lisibles, messages d’erreur compréhensibles, états vides, états de succès et écrans cohérents.

### Pôle MERISE / données / RGPD

Le pôle MERISE est responsable de la cohérence des données. Il rédige les règles de gestion, le dictionnaire de données, le MCD, le MLD et le script SQL. Il prépare aussi les données fictives et le CSV de test.

Il doit intégrer une réflexion RGPD minimale : quelles données personnelles sont stockées, pourquoi, qui y accède, quelles données sont obligatoires, quelles données doivent être évitées, comment gérer le consentement marketing et comment limiter les risques.

### Pôle Backend PHP / Slim

Le pôle backend construit l’API REST. Il crée les routes, interroge PostgreSQL avec PDO, renvoie des réponses JSON et traite les imports CSV. Il doit organiser le code proprement, même si le projet reste court.

Routes minimales attendues :

`POST /login` pour la connexion.

`GET /contacts` pour récupérer la liste des contacts.

`GET /contacts/{id}` pour récupérer une fiche contact.

`POST /imports/weezevent` pour importer un fichier CSV.

`GET /stats/dashboard` pour récupérer les indicateurs du tableau de bord.

`GET /segments` pour lister les segments.

`GET /segments/{id}/contacts` pour récupérer les contacts d’un segment.

`GET /exports/brevo.csv` ou `POST /exports/brevo` pour préparer l’export Brevo.

### Pôle Frontend HTML / Tailwind / JavaScript

Le pôle frontend construit les pages du dashboard sans framework JavaScript. Il utilise HTML, Tailwind CSS et JavaScript vanilla. Il doit manipuler le DOM, appeler l’API avec `fetch()`, afficher les données et gérer les actions utilisateur.

Écrans minimaux attendus :

écran de connexion ;

dashboard principal ;

écran d’import CSV ;

liste des contacts ;

fiche contact ;

liste des segments ;

écran d’export Brevo.

Le JavaScript doit être organisé proprement, par exemple avec des fichiers séparés : `api.js`, `contacts.js`, `dashboard.js`, `import.js`, `segments.js`. Le code doit rester lisible et commenté lorsque c’est nécessaire.

### Pôle SISR / déploiement / sécurité / automatisation

Le pôle SISR est responsable de l’environnement technique. Il met en place Docker Compose, PostgreSQL, Adminer en développement, le backend PHP/Slim, éventuellement Nginx, les volumes, les variables d’environnement et la documentation de lancement.

Il doit aussi travailler sur les sauvegardes et l’exploitation : script de sauvegarde PostgreSQL, procédure de restauration, logs, gestion des erreurs, protection des clés API, séparation développement / production, checklist sécurité et schéma d’infrastructure.

Les SISR peuvent également contribuer à la recette, à la documentation, au maquettage, aux tests et à la gestion de projet.

## 9. RACI simplifié

| Activité                 | Pilotage | UX/UI | MERISE | Backend | Frontend | SISR |
| ------------------------ | -------- | ----- | ------ | ------- | -------- | ---- |
| Définition du MVP        | R        | C     | C      | C       | C        | C    |
| WBS / planning / backlog | R        | I     | I      | I       | I        | C    |
| Charte graphique         | I        | R     | I      | I       | C        | I    |
| Maquettes                | I        | R     | C      | I       | C        | I    |
| Règles de gestion        | C        | I     | R      | C       | I        | I    |
| MCD / MLD                | I        | I     | R      | C       | I        | C    |
| Script SQL               | I        | I     | R      | C       | I        | C    |
| API Slim                 | I        | I     | C      | R       | C        | I    |
| Import CSV               | I        | I     | C      | R       | C        | C    |
| Interface HTML/Tailwind  | I        | C     | I      | C       | R        | I    |
| JavaScript / fetch       | I        | I     | I      | C       | R        | I    |
| Export Brevo             | I        | I     | C      | R       | C        | C    |
| Docker DEV               | I        | I     | C      | C       | I        | R    |
| Sécurité / secrets       | C        | I     | C      | C       | I        | R    |
| Sauvegardes              | I        | I     | C      | I       | I        | R    |
| Tests recette            | R        | C     | C      | C       | C        | C    |
| Démo finale              | R        | R     | R      | R       | R        | R    |

R signifie responsable. C signifie contributeur ou consulté. I signifie informé.

## 10. Rythme quotidien recommandé

Chaque journée suit un rythme commun.

De 9h00 à 9h30, la classe fait un point projet : objectifs du jour, blocages, dépendances, priorités. Le binôme pilotage anime ce moment.

De 9h30 à 12h30, les pôles travaillent sur leurs productions principales.

De 12h30 à 13h00, une intégration courte est organisée. Chaque groupe indique ce qui est prêt, ce qui bloque et ce dont il a besoin.

De 14h00 à 16h30, les groupes poursuivent la production, l’intégration et les tests.

De 16h30 à 17h00, le backlog et le planning sont mis à jour. Les arbitrages sont faits avec la méthode MoSCoW.

De 17h00 à 18h00, aucune grosse nouvelle fonctionnalité ne doit être lancée. Ce temps sert à stabiliser, documenter, pousser le code sur Git, nettoyer les livrables et préparer le lendemain.

## 11. Consigne centrale

Vous ne serez pas évalués uniquement sur la quantité de code produite. Vous serez évalués sur votre capacité à fonctionner comme une équipe projet : comprendre un besoin réel, prioriser, concevoir une solution cohérente, répartir les tâches, documenter vos choix, produire des livrables compatibles entre eux et présenter un prototype démontrable.

Un bon MCD, un script de sauvegarde testé, une route API propre, un écran HTML fonctionnel, une documentation de déploiement claire ou un backlog bien suivi ont tous de la valeur, à condition qu’ils contribuent réellement au projet.

Le fil rouge de la semaine est le suivant :

**Données → Base → API → Interface → Déploiement → Démonstration.**
