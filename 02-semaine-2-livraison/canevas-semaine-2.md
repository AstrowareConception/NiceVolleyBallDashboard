# Deuxième semaine intensive — Livraison du projet Nice Volley Ball Dashboard

## 1. Objectif général de la semaine

Cette deuxième semaine intensive a pour objectif de transformer le MVP réalisé lors de la première semaine en une version livrable au client.

Lors de la première semaine, un premier dashboard a été développé. Il permet déjà de récupérer des événements Weezevent, d’extraire et traiter des données, d’identifier par exemple les nouveaux spectateurs venus au dernier événement, puis d’envoyer une liste vers Brevo. Le projet a été présenté au club et validé dans son état de MVP.

La deuxième semaine doit maintenant permettre de terminer proprement le produit, de prendre en compte les remarques d’Alexandre, de stabiliser le fonctionnement, de documenter l’application, de tester sérieusement les parcours critiques et de préparer une livraison exploitable par le club.

Le projet concerne à la fois les étudiants SLAM et SISR. Les SLAM interviendront principalement sur l’application, les données, les API, le frontend et le backend. Les SISR interviendront principalement sur le déploiement, le VPS, Docker, la sécurité, les sauvegardes, les logs, l’automatisation et l’exploitation du service. Certains travaux sont transversaux : gestion de projet, tests, documentation, recette client, RGPD et qualité des données.

## 2. Résultat attendu en fin de semaine

À la fin de la semaine, l’objectif est de disposer d’une version livrable comprenant :

* une application fonctionnelle ;
* une base de données mise à jour ;
* une prise en compte des demandes prioritaires du client ;
* un environnement de déploiement propre ;
* une procédure de déploiement ;
* une documentation technique ;
* une documentation utilisateur ;
* des tests end-to-end ;
* une stratégie de sauvegarde et restauration ;
* une gestion correcte des secrets ;
* une démonstration finale ;
* un bilan clair des fonctionnalités livrées et des limites restantes.

Le résultat idéal est une application accessible sur un VPS, avec une URL sécurisée en HTTPS, une procédure de mise à jour automatisée ou semi-automatisée, une base sauvegardée, des logs exploitables et une recette client documentée.

Si un blocage externe empêche la mise en production complète, l’objectif minimal sera une préproduction correctement déployée, documentée, testée, et prête à être basculée en production avec le client.

## 3. Rappels des demandes client à intégrer

Les retours d’Alexandre doivent être repris collectivement en début de semaine et transformés en micro-cahier des charges.

Demandes déjà identifiées :

* prise en compte du RGPD ;
* réflexion sur la durée de conservation des données, avec une hypothèse de deux ans à valider ;
* possibilité d’alerter ou de contacter les personnes avant suppression, anonymisation ou expiration du consentement ;
* export de l’intégralité des contacts présents ou inscrits à un événement ;
* identification des invitations ;
* distinction entre invitations utilisées et invitations non utilisées ;
* prise en compte des tickets offerts à chaque match ;
* prise en compte de la présence réelle lorsque l’information est disponible via l’outil de contrôle d’accès ;
* gestion du consentement ;
* désinscription d’un contact ne souhaitant plus recevoir d’emails ;
* exclusion des contacts désinscrits des synchronisations Brevo.

Ces demandes doivent être reformulées proprement. Certaines relèvent du développement applicatif, certaines de la base de données, certaines du RGPD, certaines des tests et certaines de l’exploitation.

## 4. Découpage en pôles

### Pôle 1 — Gestion de projet

Effectif recommandé : 2 étudiants, idéalement 1 SLAM et 1 SISR.

Ce pôle est garant de l’organisation. Il ne remplace pas le professeur et ne décide pas seul, mais il maintient le cadre de travail.

Responsabilités :

* animer les points quotidiens ;
* formaliser le micro-cahier des charges ;
* maintenir le backlog ;
* produire ou mettre à jour le WBS ;
* produire ou mettre à jour le RACI ;
* produire ou mettre à jour le Gantt ;
* produire ou mettre à jour le PERT ;
* suivre les risques ;
* suivre les arbitrages MoSCoW ;
* préparer la démonstration finale ;
* centraliser les livrables ;
* préparer le bilan de fin de semaine.

Livrables :

* micro-cahier des charges V2 ;
* backlog priorisé ;
* WBS ;
* RACI ;
* Gantt ;
* PERT simplifié ;
* compte rendu quotidien ;
* bilan final ;
* support de restitution.

### Pôle 2 — Migration / cadrage fonctionnel / données

Effectif recommandé : 2 étudiants au début de semaine.

Ce pôle est temporaire. Il travaille surtout lundi et mardi matin. Une fois les choix stabilisés, ses membres peuvent rejoindre le développement, les tests ou la documentation.

Responsabilités :

* relire l’existant produit en première semaine ;
* identifier ce qui doit évoluer ;
* reprendre les demandes d’Alexandre ;
* mettre à jour le modèle de données ;
* produire les évolutions du MCD et du MLD ;
* définir les migrations SQL nécessaires ;
* préciser les nouvelles règles de gestion ;
* clarifier les écrans à modifier ;
* clarifier les routes API à créer ou modifier ;
* produire un document interne d’évolution V1 vers V2 ;
* préparer les cas de test associés aux nouvelles règles.

Sujets prioritaires :

* consentement ;
* désinscription ;
* durée de conservation ;
* exports par événement ;
* invitations ;
* présence effective ou non ;
* tickets offerts ;
* synchronisation Brevo ;
* traçabilité des traitements.

Livrables :

* document d’évolution V1 vers V2 ;
* MCD mis à jour ;
* MLD mis à jour ;
* migrations SQL ;
* règles de gestion ;
* mapping entre besoins, écrans et routes API ;
* liste des impacts sur le backend et le frontend.

### Pôle 3 — Développement applicatif

Effectif recommandé : 2 à 3 étudiants SLAM, éventuellement 4 si l’effectif le permet.

Ce pôle réalise les développements restants sur le dashboard.

Responsabilités :

* faire évoluer le backend PHP/Slim ;
* faire évoluer les routes API ;
* faire évoluer le frontend HTML/Tailwind/JavaScript ;
* intégrer les nouvelles règles métier ;
* connecter les écrans aux routes API ;
* corriger les bugs ;
* améliorer l’ergonomie ;
* stabiliser les flux Weezevent et Brevo ;
* travailler avec le pôle migration sur le modèle de données ;
* travailler avec le pôle test sur les anomalies.

Fonctionnalités prioritaires :

* export complet des contacts d’un événement ;
* identification des nouveaux spectateurs d’un événement ;
* gestion des invitations ;
* distinction invitation utilisée / non utilisée ;
* traitement des tickets offerts ;
* gestion du consentement ;
* désinscription ;
* exclusion des désinscrits des exports ou synchronisations Brevo ;
* amélioration des messages utilisateur ;
* historique des synchronisations ;
* logs fonctionnels visibles ou consultables.

Livrables :

* routes API finalisées ;
* écrans finalisés ;
* correctifs ;
* commits propres ;
* documentation des endpoints ;
* changelog applicatif ;
* version stable prête à déployer.

### Pôle 4 — Déploiement / infrastructure / automatisation

Effectif recommandé : 2 à 3 étudiants, idéalement 2 SISR et 1 SLAM.

Ce pôle est responsable du passage de l’environnement local vers un environnement livrable.

Responsabilités :

* accompagner le client dans le choix ou la location du VPS ;
* vérifier que le VPS appartient bien au client ou à l’organisation ;
* configurer les accès SSH ;
* sécuriser l’accès initial au serveur ;
* installer Docker et Docker Compose ;
* préparer l’environnement de production ;
* configurer les variables d’environnement ;
* protéger les secrets ;
* configurer le reverse proxy ;
* configurer HTTPS ;
* préparer la procédure de déploiement ;
* mettre en place un pipeline de déploiement automatique ou semi-automatique ;
* mettre en place les sauvegardes ;
* tester la restauration ;
* préparer les logs ;
* proposer une supervision minimale ;
* documenter l’exploitation.

Livrables :

* VPS configuré ;
* accès SSH sécurisés ;
* Docker production ;
* fichier compose de production ;
* reverse proxy ;
* HTTPS ;
* pipeline de déploiement ;
* script de sauvegarde ;
* procédure de restauration ;
* documentation d’exploitation ;
* checklist sécurité ;
* schéma d’infrastructure à jour.

Points de vigilance :

* ne jamais versionner les clés API ;
* ne jamais stocker les mots de passe en clair ;
* ne pas utiliser un compte personnel étudiant pour porter l’infrastructure du client ;
* prévoir la remise des accès au client ;
* documenter précisément les commandes d’administration ;
* tester le redémarrage complet du service.

### Pôle 5 — Tests end-to-end / recette / documentation finale

Effectif recommandé : 2 étudiants, idéalement un profil rigoureux pouvant dialoguer avec SLAM et SISR.

Ce pôle démarre dès lundi. Il ne doit pas attendre que tout soit terminé.

Responsabilités :

* définir les scénarios de test ;
* préparer les jeux d’essai ;
* tester les parcours utilisateur ;
* tester les flux API ;
* tester les erreurs ;
* tester les cas limites ;
* vérifier les corrections ;
* produire le rapport de test ;
* rédiger la documentation utilisateur ;
* contribuer à la documentation technique ;
* préparer la recette client ;
* préparer le support de démonstration ;
* vérifier que les preuves sont conservées.

Scénarios prioritaires :

* synchroniser les événements Weezevent ;
* consulter la liste des événements ;
* sélectionner un événement ;
* exporter tous les contacts d’un événement ;
* identifier les nouveaux spectateurs ;
* identifier les invitations ;
* distinguer les invitations présentes et absentes ;
* exclure un contact désinscrit des envois Brevo ;
* envoyer une liste vers Brevo ;
* vérifier le résultat côté Brevo ;
* tester un échec d’API ;
* vérifier les logs ;
* vérifier la sauvegarde ;
* vérifier la restauration ;
* vérifier le fonctionnement après déploiement.

Livrables :

* plan de test ;
* jeux d’essai ;
* rapport de test ;
* anomalies détectées ;
* suivi des corrections ;
* guide utilisateur ;
* guide d’administration ;
* support de recette ;
* procès-verbal ou compte rendu de validation finale.

## 5. Première matinée — cadrage collégial obligatoire

La première matinée doit être menée collectivement. Aucun groupe ne doit commencer à coder ou déployer sans avoir repris le contexte.

### Objectif de la matinée

Reconstituer collectivement :

* ce qui a été fait lors de la première semaine ;
* ce qui fonctionne réellement ;
* ce qui a été présenté au club ;
* ce qu’Alexandre a validé ;
* ce qu’Alexandre a demandé de modifier ou compléter ;
* ce qui doit absolument être livré ;
* ce qui peut être reporté ;
* ce qui est hors périmètre.

### Déroulé proposé sur 4 heures

#### 1. Rappel du projet et du MVP livré en juin — 30 minutes

Présentation rapide :

* contexte du club ;
* problème initial ;
* outils existants ;
* objectifs du dashboard ;
* MVP réalisé ;
* validation par le club ;
* limites actuelles.

#### 2. Démonstration technique de l’existant — 45 minutes

Reprise du projet existant :

* lancement local Docker ;
* structure du dépôt ;
* backend ;
* frontend ;
* base de données ;
* flux Weezevent ;
* flux Brevo ;
* fonctionnalités terminées ;
* fonctionnalités partielles ;
* points fragiles.

#### 3. Reprise des retours client — 45 minutes

Travail collectif sur les remarques d’Alexandre :

* RGPD ;
* conservation deux ans ;
* consentement ;
* désinscription ;
* export par événement ;
* invitations ;
* tickets offerts ;
* présence réelle ;
* besoins de livraison.

Chaque remarque doit être transformée en exigence claire.

#### 4. Micro-cahier des charges V2 — 60 minutes

La classe formalise :

* objectifs de la semaine ;
* périmètre fonctionnel ;
* priorités MoSCoW ;
* règles de gestion ;
* contraintes techniques ;
* contraintes RGPD ;
* livrables attendus ;
* critères de recette.

Le document doit être court, mais exploitable.

#### 5. Constitution des pôles — 30 minutes

Répartition des étudiants :

* gestion de projet ;
* migration / données ;
* développement ;
* déploiement ;
* tests / documentation.

Chaque étudiant doit connaître son rôle principal, son rôle secondaire et les livrables attendus.

#### 6. Backlog initial et plan de journée — 30 minutes

Création des premiers tickets :

* tâches prioritaires ;
* responsables ;
* dépendances ;
* critères d’acceptation ;
* échéance ;
* preuves attendues.

À la fin de la matinée, chaque pôle doit pouvoir commencer immédiatement.

## 6. Planning grosse maille de la semaine

### Lundi après-midi — audit, cadrage final et préparation technique

Objectif : transformer les décisions du matin en tâches concrètes.

Le pôle gestion de projet finalise le micro-cahier des charges, le MoSCoW, le backlog, le WBS, le RACI et le planning de la semaine.

Le pôle migration analyse l’impact des nouvelles demandes sur la base de données, les routes API et les écrans. Il commence le MCD/MLD V2 et prépare les migrations SQL.

Le pôle développement relance le projet, vérifie l’état du code, identifie les zones à modifier et commence les premiers correctifs simples.

Le pôle déploiement prépare le plan d’infrastructure, la liste des accès nécessaires, les choix VPS, la stratégie Docker production et la stratégie de déploiement.

Le pôle test écrit les premiers scénarios de recette à partir du cahier des charges.

Livrables lundi soir :

* micro-cahier des charges V2 ;
* backlog priorisé ;
* MCD/MLD V2 provisoire ;
* plan de déploiement ;
* premiers scénarios de test ;
* liste des accès nécessaires ;
* risques bloquants identifiés.

### Mardi matin — modèle de données, migrations et premiers développements

Objectif : stabiliser les fondations.

Le pôle migration finalise les règles de gestion et les migrations nécessaires. Les choix doivent être validés rapidement afin de ne pas bloquer le développement.

Le pôle développement commence les fonctionnalités prioritaires : consentement, désinscription, export par événement, invitations et présence.

Le pôle déploiement prépare l’environnement serveur ou, si le VPS n’est pas encore disponible, une préproduction locale ou distante équivalente.

Le pôle test prépare les jeux d’essai permettant de tester les nouveaux cas : événement avec spectateurs, événement avec invitations, contacts désinscrits, contacts avec consentement actif ou absent.

Livrables mardi midi :

* migrations SQL prêtes ;
* règles de gestion validées ;
* jeux d’essai prêts ;
* premières routes modifiées ;
* plan VPS clarifié ;
* premiers tests fonctionnels écrits.

### Mardi après-midi — développement des fonctionnalités prioritaires

Objectif : produire les principales fonctionnalités métier attendues par le client.

Priorités développement :

* exporter tous les contacts d’un événement ;
* distinguer inscrits, présents et absents si les données le permettent ;
* identifier les invitations ;
* gérer les tickets offerts ;
* gérer le consentement ;
* gérer la désinscription ;
* empêcher l’envoi Brevo pour les contacts désinscrits ;
* afficher des messages clairs dans l’interface.

Le pôle déploiement commence la configuration réelle du VPS si les accès sont disponibles. Sinon, il prépare le pipeline, les fichiers de production et la documentation.

Le pôle test exécute les premiers tests sur les fonctionnalités terminées et remonte les anomalies.

Livrables mardi soir :

* premières fonctionnalités métier opérationnelles ;
* migrations appliquées en environnement de test ;
* anomalies détectées ;
* configuration de déploiement commencée ;
* documentation technique mise à jour.

### Mercredi matin — intégration applicative et préproduction

Objectif : intégrer les fonctionnalités entre elles.

Le développement doit relier frontend, backend et base de données. Les écrans ne doivent plus être isolés. Chaque fonctionnalité prioritaire doit être testable de bout en bout.

Le pôle déploiement vise une première préproduction accessible ou, au minimum, un environnement de production simulé complet.

Le pôle test commence les tests end-to-end complets.

Le pôle gestion de projet analyse l’écart entre prévu et réalisé et arbitre les fonctionnalités à conserver, simplifier ou abandonner.

Livrables mercredi midi :

* parcours métier principaux testables ;
* première préproduction ou environnement prod simulé ;
* tests end-to-end commencés ;
* backlog réajusté ;
* liste des bugs bloquants.

### Mercredi après-midi — stabilisation, sécurité et correction des anomalies

Objectif : arrêter la dispersion et rendre le produit stable.

Priorités :

* corriger les bugs bloquants ;
* vérifier les cas limites ;
* sécuriser les secrets ;
* vérifier les droits d’accès ;
* vérifier les logs ;
* vérifier la sauvegarde ;
* préparer la restauration ;
* renforcer les messages utilisateur ;
* finaliser les écrans prioritaires.

Le pôle test doit être très actif : il valide ou refuse les fonctionnalités selon les critères de recette.

Livrables mercredi soir :

* version intégrée ;
* bugs bloquants identifiés et priorisés ;
* sauvegarde fonctionnelle ;
* logs exploitables ;
* documentation de déploiement en cours ;
* documentation utilisateur commencée.

### Jeudi matin — gel fonctionnel et recette interne

Objectif : figer le périmètre.

À partir de jeudi matin, aucune nouvelle grosse fonctionnalité ne doit être lancée sans arbitrage. La priorité devient la livraison.

Le pôle gestion de projet organise une recette interne. Le pôle test exécute les scénarios complets. Le pôle développement corrige les anomalies. Le pôle déploiement finalise l’environnement cible, HTTPS, pipeline, sauvegardes et procédures.

Livrables jeudi midi :

* périmètre final gelé ;
* rapport de recette interne ;
* liste des corrections finales ;
* environnement cible presque prêt ;
* documentation utilisateur avancée ;
* documentation d’exploitation avancée.

### Jeudi après-midi — préparation livraison

Objectif : rendre la solution présentable, installable, exploitable et transférable.

Travaux prioritaires :

* correction des derniers bugs ;
* test du déploiement depuis zéro ;
* test de sauvegarde ;
* test de restauration ;
* vérification des secrets ;
* finalisation du guide utilisateur ;
* finalisation du guide d’exploitation ;
* préparation du support de présentation ;
* préparation du bilan des limites ;
* préparation de la recette client.

Livrables jeudi soir :

* version candidate à la livraison ;
* documentation complète ;
* rapport de test ;
* procédure de déploiement ;
* procédure de sauvegarde/restauration ;
* support de démo ;
* checklist de livraison.

### Vendredi matin — répétition générale et livraison technique

Objectif : préparer la démonstration finale sans improvisation.

La classe réalise une répétition complète :

* accès à l’application ;
* synchronisation Weezevent ;
* affichage des événements ;
* export des contacts d’un événement ;
* identification des nouveaux spectateurs ;
* gestion des invitations ;
* désinscription / consentement ;
* envoi vers Brevo ;
* consultation des logs ;
* sauvegarde ;
* explication du déploiement.

Le pôle test vérifie que les scénarios sont reproductibles. Le pôle déploiement vérifie que l’application reste accessible. Le pôle gestion de projet prépare le discours final.

Livrables vendredi midi :

* démonstration prête ;
* rôles de présentation répartis ;
* environnement vérifié ;
* documentation relue ;
* rapport de test finalisé.

### Vendredi après-midi — présentation client, recette et bilan

Objectif : livrer officiellement ou préparer la livraison finale avec validation explicite.

La restitution doit être structurée :

1. rappel du besoin ;
2. rappel du MVP de juin ;
3. demandes client intégrées ;
4. démonstration de la version livrée ;
5. démonstration des aspects techniques ;
6. présentation des tests ;
7. présentation de la documentation ;
8. limites connues ;
9. points restant à traiter ;
10. validation ou retours du client.

Livrables vendredi soir :

* version livrée ;
* compte rendu de recette ;
* validation ou retours client ;
* documentation finale ;
* backlog restant ;
* bilan projet ;
* contributions individuelles ;
* éléments à intégrer au portfolio.

## 7. MoSCoW provisoire

### Must have

* Application fonctionnelle sur un environnement livrable.
* Déploiement documenté.
* HTTPS ou stratégie claire de sécurisation.
* Synchronisation Weezevent opérationnelle.
* Export complet des contacts d’un événement.
* Gestion des invitations si les données sont disponibles.
* Prise en compte de la présence réelle si les données sont disponibles.
* Gestion du consentement.
* Exclusion des contacts désinscrits des envois Brevo.
* Synchronisation ou export Brevo opérationnel.
* Logs de synchronisation.
* Tests end-to-end des parcours principaux.
* Documentation utilisateur.
* Documentation d’exploitation.
* Sauvegarde de la base.
* Test de restauration.

### Should have

* Pipeline de déploiement automatique complet.
* Rôles utilisateurs.
* Historique détaillé des synchronisations.
* Tableau de bord enrichi.
* Interface améliorée.
* Rapport RGPD simplifié.
* Alertes en cas d’échec de synchronisation.
* Procédure de purge ou anonymisation des données anciennes.

### Could have

* Supervision légère.
* Tableau d’administration des consentements.
* Export avancé filtrable.
* Tests automatisés partiels.
* Indicateurs graphiques supplémentaires.
* Mode dégradé si Brevo ou Weezevent est indisponible.

### Won’t have sauf décision contraire

* Refonte complète du site public.
* CRM complet.
* Application mobile.
* Connexion FFVB réelle.
* Paiement intégré.
* Programme de fidélité complet.
* Automatisations marketing complexes.

## 8. Points de vigilance majeurs

### Accès client et VPS

La location du VPS doit être clarifiée dès le lundi. Le serveur, le compte d’hébergement, le domaine et les accès critiques doivent appartenir au client ou être transférables proprement. Les étudiants ne doivent pas porter durablement l’infrastructure sur leurs comptes personnels.

### Secrets et clés API

Les clés Weezevent et Brevo ne doivent jamais être écrites dans le code source ni déposées dans Git. Elles doivent être stockées dans des variables d’environnement ou dans les secrets du pipeline de déploiement.

### RGPD

La règle de conservation deux ans doit être validée. Il faudra distinguer ce qui est une règle métier, ce qui est une contrainte juridique et ce qui est une décision du club. L’application doit au minimum permettre de gérer le consentement, la désinscription et l’exclusion des contacts non autorisés des campagnes.

### Données réelles

Si des données réelles sont utilisées, elles doivent être manipulées avec prudence. Les exports, captures d’écran, jeux de test et démonstrations ne doivent pas exposer inutilement des données personnelles.

### Livraison réaliste

La priorité est de livrer un service stable et utile, pas d’ajouter de nouvelles fonctionnalités jusqu’au dernier moment. Le gel fonctionnel doit intervenir au plus tard jeudi matin.

## 9. Définition de “livré”

Le projet pourra être considéré comme livré si :

* l’application est accessible dans un environnement cible ;
* les fonctionnalités prioritaires sont démontrables ;
* le client peut comprendre comment utiliser le service ;
* les données sont sauvegardées ;
* une restauration a été testée ou documentée ;
* les secrets sont protégés ;
* les logs principaux sont accessibles ;
* le déploiement est documenté ;
* les limites restantes sont clairement indiquées ;
* le client valide la version ou formule une liste de réserves.

## 10. Organisation quotidienne recommandée

Chaque journée doit suivre un rythme fixe.

Début de matinée :

* point projet ;
* objectifs du jour ;
* blocages ;
* décisions à prendre ;
* mise à jour du backlog.

Milieu de journée :

* point rapide entre pôles ;
* dépendances ;
* besoins d’intégration.

Fin de journée :

* démonstration interne courte ;
* mise à jour du backlog ;
* anomalies ;
* commits ;
* documentation ;
* bilan du jour.

Aucune journée ne doit se terminer sans une trace écrite de ce qui a été fait, de ce qui bloque et de ce qui doit être traité le lendemain.
