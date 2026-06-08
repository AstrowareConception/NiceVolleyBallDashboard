# Semaine intensive — Projet Nice Volley Ball Dashboard

## 1. Objectif général de la semaine

Pendant cette semaine intensive, vous allez travailler sur un projet réel proposé par le Nice Volley Ball. Le club dispose aujourd’hui de plusieurs outils séparés : Weezevent pour la billetterie, Brevo pour les newsletters et les contacts, des fichiers Excel ou Google Sheets pour certains suivis manuels, ainsi que des outils institutionnels comme l’extranet FFVB. Ces outils sont utiles, mais ils ne communiquent pas suffisamment entre eux.

Le problème principal est donc la dispersion des données. Le club ne dispose pas d’une vision claire du parcours d’un supporter : première venue, nombre de matchs vus, intérêt pour les abonnements, présence en famille, potentiel merchandising, réaction aux campagnes ou retour après un événement. Les communications restent trop générales et les relances ne sont pas assez automatisées.

L’objectif de la semaine n’est pas de développer un CRM complet. L’objectif est de produire un **prototype fonctionnel démontrable** d’un dashboard interne permettant de récupérer automatiquement les données de billetterie depuis l’API REST de Weezevent, de les stocker proprement, de les consulter, de générer quelques indicateurs simples, de segmenter les contacts et de synchroniser certaines données avec Brevo via son API REST.

Le projet est majoritairement orienté SLAM, mais les étudiants SISR ont un rôle important sur l’environnement technique, le déploiement, la sécurité, les sauvegardes, les automatisations et la documentation d’exploitation.

## 2. Résultat attendu vendredi

À la fin de la semaine, la classe devra pouvoir présenter un prototype cohérent.

Le scénario de démonstration visé est le suivant :

Un gestionnaire du club accède à une interface web interne. Il déclenche une synchronisation avec l’API Weezevent. Le système récupère les données de billetterie, crée ou met à jour des contacts dans une base PostgreSQL, détecte certains doublons simples et affiche un bilan de synchronisation. Le gestionnaire peut ensuite consulter la liste des contacts, ouvrir une fiche contact, visualiser quelques indicateurs dans un tableau de bord, afficher des segments simples comme « nouveaux visiteurs », « supporters réguliers » ou « abonnés potentiels », puis synchroniser un segment vers Brevo via son API REST.

En parallèle, les étudiants SISR devront être capables de montrer comment l’application est lancée, comment les services sont organisés, comment les variables sensibles sont protégées, comment la base est sauvegardée, où sont les logs et quelles précautions sont prévues pour un futur déploiement.

## 3. Pile technique retenue

La pile technique doit rester adaptée au niveau actuel de la classe. Le projet ne doit pas reposer sur React, Vue ou Angular. L’objectif est de renforcer les bases : HTML, CSS, JavaScript, PHP, SQL, API REST, Docker et déploiement.

La pile retenue est la suivante :

Côté interface utilisateur, le projet utilisera **HTML**, **Tailwind CSS** et **JavaScript vanilla**. Les pages seront donc construites avec du HTML classique, stylisées avec Tailwind, et rendues dynamiques avec du JavaScript écrit par les étudiants. Les appels au backend se feront avec `fetch()`. Le JavaScript devra gérer l’affichage des contacts, les formulaires, les messages d’erreur, les tableaux, les cartes statistiques et les actions utilisateur.

Côté backend, le projet utilisera **PHP avec le micro-framework Slim**. Slim permettra de créer une API REST légère, structurée et compréhensible. Il servira à définir les routes, recevoir les requêtes HTTP, interroger la base de données, communiquer avec les API Weezevent et Brevo, puis renvoyer des réponses JSON.

Côté base de données, le projet utilisera **PostgreSQL**. La base contiendra les contacts, événements, achats, synchronisations, segments, campagnes, utilisateurs et rôles éventuels. Le modèle devra être issu d’une vraie réflexion MERISE : règles de gestion, dictionnaire de données, MCD, MLD, puis script SQL.

Côté déploiement, le projet utilisera **Docker Compose**. Les SISR devront mettre en place un environnement de développement comprenant au minimum le backend PHP/Slim, PostgreSQL, Adminer et éventuellement Nginx. Une réflexion sera également menée sur un environnement de production plus sécurisé avec reverse proxy, HTTPS, variables d’environnement, sauvegardes et logs.

Côté intégrations externes, **Weezevent** et **Brevo** sont au cœur du projet. L’objectif est de comprendre comment s’authentifier auprès de leurs API REST, récupérer des données, créer ou mettre à jour des contacts, gérer des attributs et synchroniser des informations entre les différents systèmes.

## 4. Organisation générale des équipes

La classe sera répartie en pôles de travail. Les groupes pourront évoluer au cours de la semaine, mais chaque pôle doit avoir des responsabilités claires.

Un binôme assurera le pilotage projet. Ce binôme ne sera pas exempté de production technique ou documentaire, mais il sera garant de l’organisation : WBS, RACI, MoSCoW, Gantt, PERT simplifié, backlog, suivi des tâches, arbitrages et préparation de la démonstration finale. Idéalement, ce binôme sera composé d’un étudiant SLAM et d’un étudiant SISR.

Un pôle UX / UI travaillera sur la charte graphique, les parcours utilisateurs et les maquettes. Ce pôle devra produire des écrans réalistes, puis aider à leur intégration en HTML / Tailwind. Ce pôle peut accueillir des étudiants SISR, car le maquettage, l’ergonomie et la documentation visuelle sont des tâches transversales.

Un pôle MERISE / données / RGPD travaillera sur les règles de gestion, le dictionnaire de données, le MCD, le MLD, le script SQL et les données de test. Ce pôle est prioritaire en début de semaine, car le backend dépend directement de la structure de la base.

Un pôle backend PHP / Slim travaillera sur l’API REST. Il devra gérer la connexion à PostgreSQL, les routes, les appels aux API Weezevent et Brevo, la création et la consultation des contacts, les statistiques et les segments.

Un pôle frontend HTML / Tailwind / JavaScript travaillera sur les pages visibles du dashboard. Il devra produire des interfaces simples, propres et fonctionnelles, puis les connecter à l’API avec `fetch()`.

Un pôle SISR / déploiement / sécurité / automatisation travaillera sur Docker, l’environnement technique, les sauvegardes, les logs, les variables d’environnement, la sécurité minimale et la documentation d’exploitation.

## 5. Méthode projet imposée

Le projet doit permettre de mettre en pratique les outils de gestion de projet vus en cours. Ces outils ne doivent pas rester théoriques : ils doivent aider la classe à organiser réellement le travail.

Le binôme de pilotage devra produire un WBS pour découper le projet en lots et sous-tâches. Il devra aussi produire un MoSCoW pour distinguer les fonctionnalités indispensables, importantes, optionnelles et hors périmètre. Un RACI devra permettre de savoir qui est responsable, qui contribue, qui est consulté et qui doit être informé. Un Gantt simplifié devra présenter la planification de la semaine. Un PERT simplifié devra faire apparaître les dépendances critiques, notamment entre MERISE, base de données, API, front et déploiement.

Le backlog devra être tenu à jour chaque jour. Les tâches devront être rédigées clairement, avec un responsable, une priorité et un critère d’acceptation. Une tâche comme « faire le backend » est trop vague. Une tâche correcte serait plutôt : « Créer la route GET /contacts qui renvoie la liste des contacts au format JSON avec id, nom, prénom, email et segment principal. »

## 6. MoSCoW du projet

Les fonctionnalités indispensables pour vendredi sont les suivantes : disposer d’un modèle de données cohérent, d’une base PostgreSQL fonctionnelle, d’une connexion à l’API Weezevent, d’une API Slim capable de lire et écrire les contacts, d’une interface HTML / Tailwind affichant les contacts, d’une fiche contact, d’un dashboard avec quelques statistiques, d’une segmentation simple, d’une synchronisation Brevo via API REST, d’un environnement Docker de développement et d’une documentation minimale.

Les fonctionnalités importantes mais non bloquantes sont les suivantes : authentification simple, gestion de deux rôles, détection basique des doublons, historique des synchronisations, sauvegarde automatique de la base, interface plus soignée, messages d’erreur clairs et synchronisation bidirectionnelle avec Brevo.

Les fonctionnalités optionnelles sont les suivantes : récupération avancée des événements Weezevent, environnement de production complet avec Nginx et HTTPS, graphiques avancés, tests automatisés, CI/CD, historique détaillé des campagnes et restauration automatisée documentée.

Les éléments hors périmètre pour cette semaine sont les suivants : CRM complet, application mobile, espace supporter public, paiement intégré, connexion FFVB réelle, programme de fidélité complet, refonte du site du club et automatisations marketing avancées en production.

## 7. Planning détaillé de la semaine

### Lundi matin — Cadrage, méthode projet et MVP

La première demi-journée sert à poser le cadre. La classe reprend le contexte du Nice Volley Ball, les travaux déjà réalisés et l’objectif final du prototype. Le binôme de pilotage anime la clarification du MVP. Il formalise les premières versions du MoSCoW, du WBS et du backlog.

Le pôle MERISE démarre immédiatement. Il doit identifier les principales entités : contact, événement, achat, synchronisation, segment, campagne, utilisateur, rôle et consentement éventuel. Il commence les règles de gestion et prépare une première version du MCD.

Le pôle technique confirme la pile retenue : HTML, Tailwind, JavaScript vanilla, PHP Slim, PostgreSQL, Docker Compose, API Weezevent et API Brevo.

Livrables attendus lundi midi : MVP validé, groupes constitués, MoSCoW V1, WBS V1, premières entités identifiées, backlog initial.

### Lundi après-midi — Squelettes techniques et premières conceptions

Le pôle MERISE finalise un MCD V1 et commence le MLD. Le pôle backend crée le projet Slim, prépare l’arborescence, configure Composer, met en place une première route de test et prépare la connexion à PostgreSQL. Le pôle frontend prépare les premières pages HTML, installe ou configure Tailwind, crée une structure de fichiers claire et commence les écrans statiques.

Le pôle SISR prépare un premier docker-compose de développement avec PostgreSQL, Adminer et le backend PHP. Il documente les commandes de lancement. Le pôle UX produit les premières maquettes du dashboard, de la liste contacts, de la fiche contact et de l’écran de synchronisation.

Livrables attendus lundi soir : dépôt Git initial, docker-compose DEV initial, projet Slim lancé, première page HTML/Tailwind, MCD V1, MLD V1, maquettes V1.

### Mardi matin — Base de données et premières routes API

Le mardi matin est consacré au socle de données. Le pôle MERISE produit le script SQL de création de base et un jeu de données fictif.

Le backend développe les premières routes : liste des contacts, détail d’un contact, création d’un contact de test et route de santé de l’API. Il commence également l’étude de l’authentification et des endpoints de l’API Weezevent.

Le frontend commence à appeler l’API avec `fetch()` pour afficher une réponse simple dans une page HTML.

Le SISR stabilise Docker : variables d’environnement, volume PostgreSQL, accès Adminer en développement, documentation d’installation.

Livrables attendus mardi midi : script SQL fonctionnel, base PostgreSQL créée, premières routes API, premier appel `fetch()` depuis une page HTML.

### Mardi après-midi — Connexion à l’API Weezevent

Le mardi après-midi, le backend développe la connexion à l’API Weezevent. L’objectif est de récupérer les événements, participants ou données de billetterie nécessaires au projet, puis de les intégrer dans la base PostgreSQL.

Le frontend développe l’écran de synchronisation : bouton de synchronisation, affichage du résultat, nombre d’enregistrements récupérés et éventuelles erreurs.

Le pôle UX ajuste les maquettes selon ce qui est réellement faisable. Le binôme projet met à jour le RACI et le Gantt.

Livrables attendus mardi soir : connexion API Weezevent fonctionnelle ou partiellement fonctionnelle, synchronisation de données, tableau contacts alimenté dynamiquement, RACI V1, Gantt V1.

### Mercredi matin — Fiche contact, dashboard et segmentation

Le mercredi matin doit produire de la valeur métier. Le backend ajoute les routes permettant de récupérer une fiche contact détaillée, les statistiques du dashboard et les premiers segments. Les règles de segmentation doivent rester simples : nouveau visiteur, supporter régulier, abonné potentiel, contact inactif.

Le frontend développe la fiche contact et le tableau de bord. Les statistiques peuvent être affichées sous forme de cartes simples : nombre total de contacts, nouveaux contacts, visiteurs récurrents, abonnés potentiels et nombre de billets ou participations récupérés depuis Weezevent.

Le pôle MERISE vérifie que les routes et les données respectent le modèle. Le SISR commence les scripts de sauvegarde PostgreSQL.

Livrables attendus mercredi midi : fiche contact, route statistiques, route segments, dashboard simple, script de sauvegarde initial.

### Mercredi après-midi — Intégration Brevo

Le mercredi après-midi est consacré à Brevo. Le backend doit réussir un appel API Brevo sur un compte de test : création ou mise à jour d’un contact, ajout à une liste ou synchronisation d’un segment.

Le frontend développe l’écran de segmentation et de synchronisation : choix d’un segment, prévisualisation des contacts concernés et bouton de synchronisation vers Brevo.

Le SISR documente la gestion des clés API : fichier `.env`, variables d’environnement, exclusion du dépôt Git et règles de sécurité.

Livrables attendus mercredi soir : synchronisation Brevo fonctionnelle ou partiellement fonctionnelle, écran segments, documentation des variables sensibles, logs de synchronisation.

### Jeudi matin — Authentification, rôles et sécurité

Le jeudi matin sert à sécuriser le prototype. Le backend met en place une authentification simple si elle n’existe pas encore. L’objectif n’est pas de développer un système complexe, mais de comprendre les principes : mot de passe hashé, route de connexion, session ou token simple, protection minimale des routes sensibles.

Si le temps le permet, deux rôles sont prévus : administrateur et utilisateur restreint. L’administrateur peut lancer les synchronisations Weezevent et Brevo et consulter les données. L’utilisateur restreint peut consulter le dashboard et les contacts, mais ne peut pas déclencher les synchronisations.

Le frontend ajoute l’écran de connexion et adapte l’affichage selon le rôle si cette partie est prête. Le SISR vérifie que la base n’est pas exposée publiquement, qu’Adminer est réservé au développement, que les secrets ne sont pas versionnés et que les sauvegardes ne sont pas accessibles depuis le web.

Livrables attendus jeudi midi : connexion simple, protection minimale des routes, checklist sécurité, séparation claire entre données de test et données sensibles.

### Jeudi après-midi — Intégration, tests et documentation

À partir du jeudi après-midi, les grosses nouvelles fonctionnalités doivent être limitées. La priorité devient l’intégration. Les groupes doivent corriger les bugs, tester les parcours, stabiliser les écrans, documenter les routes API, documenter l’installation et préparer la démonstration.

Le binôme projet organise une recette interne. Le scénario testé doit être complet : lancer l’environnement, créer la base, synchroniser les données Weezevent, afficher les contacts, ouvrir une fiche contact, consulter le dashboard, générer un segment, synchroniser vers Brevo, vérifier les logs et lancer une sauvegarde.

Livrables attendus jeudi soir : version intégrée, documentation technique, documentation utilisateur courte, scénario de recette, liste des fonctionnalités réellement disponibles.

### Vendredi matin — Préparation de la démonstration finale

Le vendredi matin est consacré à la préparation de la restitution. La démonstration doit être unifiée. Il ne faut pas présenter cinq travaux indépendants, mais un projet global.

Le binôme projet prépare l’introduction : contexte, besoin, MVP, organisation, méthode de travail. Le pôle UX présente la logique des écrans. Le pôle MERISE présente le modèle de données. Le pôle backend présente l’API Slim, l’intégration Weezevent et l’intégration Brevo. Le pôle frontend présente l’interface HTML / Tailwind / JavaScript. Le pôle SISR présente Docker, le déploiement, la sécurité, les logs et les sauvegardes.

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

Le pôle UX / UI produit une charte graphique courte et des maquettes exploitables. Il travaille sur l’ergonomie du dashboard, le parcours de synchronisation, la consultation d’un contact, l’affichage des statistiques et la gestion des segments.

Il doit aussi aider à l’intégration HTML / Tailwind. Ses productions doivent donc être réalistes : composants simples, boutons clairs, tableaux lisibles, messages d’erreur compréhensibles, états vides, états de succès et écrans cohérents.

### Pôle MERISE / données / RGPD

Le pôle MERISE est responsable de la cohérence des données. Il rédige les règles de gestion, le dictionnaire de données, le M
