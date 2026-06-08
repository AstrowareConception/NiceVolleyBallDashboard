# Aide MERISE — Modéliser les données du dashboard Nice Volley Ball

## 1. Rôle du pôle MERISE dans le projet

Le pôle MERISE est responsable de la cohérence des données du projet Nice Volley Ball Dashboard. Son travail est fondamental, car toute l’application dépendra de la qualité du modèle de données. Si le modèle est mal conçu, le backend PHP/Slim devra improviser, le frontend affichera des données incohérentes et les synchronisations avec Weezevent et Brevo risqueront de produire des doublons, des pertes d’information ou des erreurs difficiles à corriger.

Le dashboard doit permettre au club de centraliser des données issues de plusieurs sources : billetterie Weezevent, campagnes ou contacts Brevo, fichiers internes et éventuellement données saisies manuellement. Ces données concernent principalement des contacts, des événements, des achats de billets, des segments marketing, des synchronisations et des utilisateurs internes.

Votre objectif n’est pas de créer une base de données gigantesque. Votre objectif est de produire un modèle simple, robuste, compréhensible et directement exploitable par les autres pôles du projet.

## 2. Ce que vous devez produire

À la fin de votre travail, vous devez fournir les livrables suivants :

* des règles de gestion claires ;
* un dictionnaire de données ;
* un MCD propre et lisible ;
* un MLD cohérent ;
* un script SQL de création de base ;
* un jeu de données de test ;
* une courte note RGPD ;
* une liste des questions à valider avec le client ou avec les autres pôles.

Ces livrables doivent être utilisables par le pôle backend. Ils ne doivent pas rester théoriques. Le script SQL doit permettre de créer une vraie base PostgreSQL. Le jeu de données doit permettre de tester les routes API, le dashboard et les segments.

## 3. Rappel rapide : MCD, MLD, MPD

Le MCD, ou Modèle Conceptuel de Données, décrit les données du point de vue métier. Il permet de représenter les entités importantes, leurs propriétés, leurs identifiants, leurs associations et leurs cardinalités. Le MCD ne dépend pas encore d’un SGBD précis. Il sert à répondre à la question : quelles informations le système doit-il représenter ?

Le MLD, ou Modèle Logique de Données, traduit le MCD en structures relationnelles. Les entités deviennent des tables, les propriétés deviennent des colonnes, les identifiants deviennent des clés primaires, et les associations deviennent soit des clés étrangères, soit des tables d’association.

Le MPD, ou Modèle Physique de Données, correspond à l’implémentation réelle dans le SGBD choisi. Dans notre projet, le SGBD est PostgreSQL. Le MPD se matérialise donc par un script SQL contenant les `CREATE TABLE`, les types de colonnes, les clés primaires, les clés étrangères, les contraintes, les index éventuels et quelques données de test.

Le chemin normal est donc :

```text
Règles de gestion → Dictionnaire de données → MCD → MLD → Script SQL PostgreSQL
```

Il ne faut pas commencer directement par les tables SQL sans avoir réfléchi aux règles métier.

## 4. Comprendre le besoin métier

Le Nice Volley Ball veut mieux connaître ses supporters et mieux utiliser ses données. Aujourd’hui, un supporter peut acheter des billets sur Weezevent, recevoir des emails via Brevo, venir plusieurs fois en salle, appartenir à une famille de licenciés, être un partenaire potentiel ou devenir un abonné possible. Mais ces informations sont dispersées.

Le dashboard doit donc permettre de répondre à des questions simples :

Combien de contacts avons-nous dans la base ?

Quels contacts viennent pour la première fois ?

Quels contacts sont déjà venus plusieurs fois ?

Quels contacts pourraient devenir abonnés ?

Quels contacts peuvent être envoyés vers Brevo pour une campagne ciblée ?

Quels événements ou matchs ont généré le plus de contacts ?

Quand une synchronisation Weezevent a-t-elle été réalisée ?

Combien de contacts ont été créés, mis à jour ou ignorés lors d’une synchronisation ?

Ces questions doivent guider votre modèle de données.

## 5. Entités probables du projet

Vous devez réfléchir aux entités nécessaires. Les entités suivantes sont fortement probables, mais vous devez les discuter et les adapter.

### Contact

Un contact représente une personne connue du club. Il peut s’agir d’un supporter, d’un visiteur occasionnel, d’un abonné potentiel, d’un licencié, d’un partenaire ou d’un prospect. Le contact est au centre du modèle.

Exemples d’informations possibles : nom, prénom, email, téléphone, source d’origine, date de création, date de dernière mise à jour, consentement marketing, identifiant Brevo éventuel.

Attention : un contact ne doit pas représenter un achat. Une personne peut avoir plusieurs achats. Il faut donc séparer `Contact` et `AchatBillet`.

### Evenement

Un événement représente un match, une soirée, une opération spéciale ou tout événement pour lequel le club souhaite suivre des participations ou des achats.

Exemples d’informations possibles : nom, date, lieu, type d’événement, identifiant Weezevent éventuel, saison.

Dans une première version, vous pouvez considérer qu’un événement est principalement un match ou une opération de billetterie récupérée depuis Weezevent.

### AchatBillet

Un achat de billet représente une transaction ou une participation associée à un contact et à un événement. Cette entité est essentielle pour connaître l’historique de présence d’un supporter.

Exemples d’informations possibles : date d’achat, quantité, montant total, type de tarif, code promotionnel, origine, identifiant Weezevent éventuel.

Attention : un achat doit être relié à un contact et à un événement. Il ne faut pas stocker tous les achats directement dans la table `Contact`.

### SynchronisationWeezevent

Une synchronisation Weezevent représente une opération de récupération de données depuis Weezevent. Elle permet de tracer quand l’application a récupéré des données, combien de lignes ont été traitées, combien de contacts ont été créés, combien ont été mis à jour et combien d’erreurs ont été rencontrées.

Exemples d’informations possibles : date de début, date de fin, statut, nombre de lignes traitées, nombre de contacts créés, nombre de contacts mis à jour, nombre d’erreurs, message d’erreur éventuel.

Cette entité est très utile pour le dashboard et pour les SISR, car elle permet de produire des logs et de vérifier l’exploitation du service.

### Segment

Un segment représente un groupe marketing ou métier. Par exemple : nouveaux visiteurs, supporters réguliers, abonnés potentiels, contacts inactifs, familles, partenaires, bénévoles.

Un contact peut appartenir à plusieurs segments. Un segment peut contenir plusieurs contacts. Il faudra donc probablement une association entre `Contact` et `Segment`.

Attention : certains segments peuvent être calculés automatiquement, par exemple “supporter régulier” si le contact a acheté au moins trois fois. D’autres peuvent être attribués manuellement. Vous devez indiquer ce que vous retenez pour le prototype.

### CampagneBrevo

Une campagne Brevo représente une action marketing préparée ou synchronisée vers Brevo. Elle peut cibler un segment donné.

Exemples d’informations possibles : nom, date de préparation, objectif, statut, identifiant Brevo éventuel, segment ciblé.

Pour le prototype, cette entité peut rester simple. Elle permet surtout de garder une trace des exports ou synchronisations vers Brevo.

### Utilisateur

Un utilisateur représente une personne qui se connecte au dashboard. Il ne faut pas confondre `Utilisateur` et `Contact`.

Un `Contact` est une personne suivie par le club dans une logique supporter ou marketing. Un `Utilisateur` est une personne interne qui utilise l’application : administrateur, chargé de communication, alternant, manager.

Exemples d’informations possibles : nom, prénom, email, mot de passe hashé, rôle, statut actif.

### Role

Un rôle représente le niveau d’autorisation d’un utilisateur. Par exemple : administrateur, gestionnaire, utilisateur restreint.

Vous pouvez modéliser le rôle de deux façons. Soit avec une table `Role`, soit avec un simple champ `role` dans la table `Utilisateur`. Pour un projet propre et évolutif, une table `Role` est préférable. Pour un prototype rapide, un champ `role` peut suffire. Vous devez justifier votre choix.

### Consentement

Le consentement permet de tracer l’accord d’un contact pour recevoir des communications marketing. Cette notion est importante dans le cadre du RGPD.

Exemples d’informations possibles : contact concerné, type de consentement, date, source, statut actif ou révoqué.

Pour le prototype, il est possible de simplifier en ajoutant un champ `marketing_consent` dans `Contact`. Mais si vous voulez être plus rigoureux, vous pouvez créer une entité `Consentement`.

## 6. Règles de gestion à formuler

Avant de dessiner le MCD, vous devez rédiger des règles de gestion. Une règle de gestion décrit une contrainte métier. Elle doit être claire, numérotée et compréhensible.

Exemples de règles possibles :

RG1 — Un contact est identifié de manière unique dans la base par un identifiant interne.

RG2 — Un contact peut être associé à zéro, un ou plusieurs achats de billets.

RG3 — Un achat de billet concerne un seul contact.

RG4 — Un achat de billet concerne un seul événement.

RG5 — Un événement peut être associé à zéro, un ou plusieurs achats de billets.

RG6 — Un contact peut appartenir à zéro, un ou plusieurs segments.

RG7 — Un segment peut regrouper zéro, un ou plusieurs contacts.

RG8 — Une campagne Brevo peut cibler un segment.

RG9 — Un segment peut être utilisé par zéro, une ou plusieurs campagnes Brevo.

RG10 — Une synchronisation Weezevent peut créer ou mettre à jour plusieurs contacts.

RG11 — Une synchronisation Weezevent peut créer ou mettre à jour plusieurs événements ou achats.

RG12 — Un utilisateur possède un rôle.

RG13 — Un rôle peut être attribué à plusieurs utilisateurs.

RG14 — Un contact peut posséder un consentement marketing actif, inactif ou inconnu.

RG15 — Un contact ne doit pas recevoir de communication marketing si son consentement est absent ou refusé.

Ces règles doivent être adaptées à votre modèle. Si vous n’êtes pas capables d’expliquer une cardinalité, c’est probablement que la règle de gestion correspondante n’est pas assez claire.

## 7. Cardinalités à discuter

Les cardinalités sont un point essentiel du MCD. Elles permettent de dire combien d’occurrences d’une entité peuvent être reliées à une autre.

### Contact — AchatBillet

Un contact peut avoir zéro ou plusieurs achats. Un achat concerne un seul contact.

Cardinalité probable :

```text
Contact (0,n) —— réaliser —— (1,1) AchatBillet
```

Pourquoi zéro ? Parce qu’un contact peut exister dans Brevo sans avoir encore acheté de billet.

### Evenement — AchatBillet

Un événement peut être lié à zéro ou plusieurs achats. Un achat concerne un seul événement.

Cardinalité probable :

```text
Evenement (0,n) —— concerner —— (1,1) AchatBillet
```

Pourquoi zéro ? Parce qu’un événement peut être créé avant que des billets aient été vendus.

### Contact — Segment

Un contact peut appartenir à zéro ou plusieurs segments. Un segment peut contenir zéro ou plusieurs contacts.

Cardinalité probable :

```text
Contact (0,n) —— appartenir —— (0,n) Segment
```

Cette relation nécessite une table d’association dans le MLD.

### Segment — CampagneBrevo

Une campagne peut cibler un segment. Un segment peut être utilisé dans plusieurs campagnes.

Cardinalité possible :

```text
Segment (0,n) —— cibler —— (1,1) CampagneBrevo
```

On peut aussi imaginer qu’une campagne cible plusieurs segments. Dans ce cas, la cardinalité devient plusieurs-à-plusieurs. Pour le prototype, il est plus simple de dire qu’une campagne cible un segment principal.

### Role — Utilisateur

Un rôle peut être attribué à plusieurs utilisateurs. Un utilisateur possède un seul rôle.

Cardinalité probable :

```text
Role (0,n) —— attribuer —— (1,1) Utilisateur
```

### Contact — Consentement

Si vous créez une entité `Consentement`, un contact peut avoir plusieurs traces de consentement dans le temps. Un consentement concerne un seul contact.

Cardinalité possible :

```text
Contact (0,n) —— donner —— (1,1) Consentement
```

Cela permet de conserver l’historique des consentements.

## 8. Dictionnaire de données : modèle à utiliser

Le dictionnaire de données doit décrire les informations importantes. Il peut être présenté sous forme de tableau.

Exemple de structure :

| Donnée            | Signification                  | Type probable | Obligatoire | Source           | Remarque                     |
| ----------------- | ------------------------------ | ------------: | ----------- | ---------------- | ---------------------------- |
| contact_id        | Identifiant interne du contact |        entier | oui         | système          | clé primaire                 |
| email             | Adresse email du contact       |         texte | oui/non     | Weezevent/Brevo  | utile pour dédoublonnage     |
| first_name        | Prénom du contact              |         texte | non         | Weezevent/Brevo  | peut être absent             |
| last_name         | Nom du contact                 |         texte | non         | Weezevent/Brevo  | peut être absent             |
| phone             | Téléphone                      |         texte | non         | Weezevent        | donnée personnelle           |
| marketing_consent | Consentement marketing         |       booléen | non         | Brevo/formulaire | RGPD                         |
| weezevent_id      | Identifiant externe Weezevent  |         texte | non         | Weezevent        | permet d’éviter les doublons |
| brevo_id          | Identifiant externe Brevo      |         texte | non         | Brevo            | utile pour synchronisation   |
| event_name        | Nom de l’événement             |         texte | oui         | Weezevent        | match ou opération           |
| purchase_amount   | Montant d’achat                |       décimal | non         | Weezevent        | indicateur commercial        |
| sync_status       | Statut d’une synchronisation   |         texte | oui         | système          | succès, erreur, partiel      |

Vous devez compléter ce dictionnaire avec vos propres données. Ne vous contentez pas des exemples.

## 9. Proposition de point de départ pour le MCD

Votre MCD peut partir de la structure suivante :

```text
CONTACT
- id_contact
- nom
- prenom
- email
- telephone
- source_origine
- consentement_marketing
- id_brevo
- date_creation
- date_mise_a_jour

EVENEMENT
- id_evenement
- nom
- date_evenement
- lieu
- type_evenement
- saison
- id_weezevent

ACHAT_BILLET
- id_achat
- date_achat
- quantite
- montant_total
- type_tarif
- code_promo
- id_weezevent_order

SEGMENT
- id_segment
- nom
- description
- type_segment

CAMPAGNE_BREVO
- id_campagne
- nom
- objectif
- date_preparation
- statut
- id_brevo_campaign

SYNCHRONISATION_WEEZEVENT
- id_synchronisation
- date_debut
- date_fin
- statut
- nb_lignes_traitees
- nb_contacts_crees
- nb_contacts_mis_a_jour
- nb_erreurs
- message_erreur

UTILISATEUR
- id_utilisateur
- nom
- prenom
- email
- mot_de_passe_hash
- actif

ROLE
- id_role
- nom
- description
```

Associations probables :

```text
CONTACT réalise ACHAT_BILLET
EVENEMENT concerne ACHAT_BILLET
CONTACT appartient à SEGMENT
SEGMENT cible CAMPAGNE_BREVO
ROLE est attribué à UTILISATEUR
SYNCHRONISATION_WEEZEVENT produit ou trace des créations/mises à jour
```

La dernière association autour de `SynchronisationWeezevent` est à discuter. Pour simplifier, vous pouvez ne pas relier chaque ligne importée à la synchronisation. Vous pouvez simplement stocker un bilan global. Pour être plus précis, vous pouvez ajouter une entité `LogSynchronisation` ou une table de liaison entre synchronisation et achats créés. Pour le prototype, un bilan global peut suffire.

## 10. Passage possible vers le MLD

À partir du MCD, vous pouvez obtenir un MLD de départ.

Exemple de tables :

```text
roles(
  id_role,
  nom,
  description
)

utilisateurs(
  id_utilisateur,
  id_role,
  nom,
  prenom,
  email,
  mot_de_passe_hash,
  actif,
  date_creation
)

contacts(
  id_contact,
  nom,
  prenom,
  email,
  telephone,
  source_origine,
  consentement_marketing,
  id_brevo,
  date_creation,
  date_mise_a_jour
)

evenements(
  id_evenement,
  nom,
  date_evenement,
  lieu,
  type_evenement,
  saison,
  id_weezevent
)

achats_billets(
  id_achat,
  id_contact,
  id_evenement,
  date_achat,
  quantite,
  montant_total,
  type_tarif,
  code_promo,
  id_weezevent_order
)

segments(
  id_segment,
  nom,
  description,
  type_segment
)

contacts_segments(
  id_contact,
  id_segment,
  date_attribution
)

campagnes_brevo(
  id_campagne,
  id_segment,
  nom,
  objectif,
  date_preparation,
  statut,
  id_brevo_campaign
)

synchronisations_weezevent(
  id_synchronisation,
  date_debut,
  date_fin,
  statut,
  nb_lignes_traitees,
  nb_contacts_crees,
  nb_contacts_mis_a_jour,
  nb_erreurs,
  message_erreur
)
```

Clés étrangères probables :

```text
utilisateurs.id_role → roles.id_role
achats_billets.id_contact → contacts.id_contact
achats_billets.id_evenement → evenements.id_evenement
contacts_segments.id_contact → contacts.id_contact
contacts_segments.id_segment → segments.id_segment
campagnes_brevo.id_segment → segments.id_segment
```

La table `contacts_segments` est nécessaire parce que la relation entre contacts et segments est une relation plusieurs-à-plusieurs.

## 11. Exemple de script SQL de départ

Ce script est un exemple de départ. Vous pouvez l’adapter selon vos choix de modélisation.

```sql
CREATE TABLE roles (
    id_role SERIAL PRIMARY KEY,
    nom VARCHAR(50) NOT NULL UNIQUE,
    description TEXT
);

CREATE TABLE utilisateurs (
    id_utilisateur SERIAL PRIMARY KEY,
    id_role INTEGER NOT NULL REFERENCES roles(id_role),
    nom VARCHAR(100) NOT NULL,
    prenom VARCHAR(100) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    mot_de_passe_hash VARCHAR(255) NOT NULL,
    actif BOOLEAN NOT NULL DEFAULT TRUE,
    date_creation TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE contacts (
    id_contact SERIAL PRIMARY KEY,
    nom VARCHAR(100),
    prenom VARCHAR(100),
    email VARCHAR(255),
    telephone VARCHAR(30),
    source_origine VARCHAR(50),
    consentement_marketing BOOLEAN,
    id_brevo VARCHAR(100),
    date_creation TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    date_mise_a_jour TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT uq_contacts_email UNIQUE(email)
);

CREATE TABLE evenements (
    id_evenement SERIAL PRIMARY KEY,
    nom VARCHAR(255) NOT NULL,
    date_evenement TIMESTAMP,
    lieu VARCHAR(255),
    type_evenement VARCHAR(50),
    saison VARCHAR(20),
    id_weezevent VARCHAR(100),
    CONSTRAINT uq_evenements_weezevent UNIQUE(id_weezevent)
);

CREATE TABLE achats_billets (
    id_achat SERIAL PRIMARY KEY,
    id_contact INTEGER NOT NULL REFERENCES contacts(id_contact) ON DELETE CASCADE,
    id_evenement INTEGER NOT NULL REFERENCES evenements(id_evenement) ON DELETE CASCADE,
    date_achat TIMESTAMP,
    quantite INTEGER NOT NULL DEFAULT 1,
    montant_total NUMERIC(10,2),
    type_tarif VARCHAR(100),
    code_promo VARCHAR(100),
    id_weezevent_order VARCHAR(100),
    CONSTRAINT uq_achats_weezevent_order UNIQUE(id_weezevent_order)
);

CREATE TABLE segments (
    id_segment SERIAL PRIMARY KEY,
    nom VARCHAR(100) NOT NULL UNIQUE,
    description TEXT,
    type_segment VARCHAR(50) NOT NULL DEFAULT 'manuel'
);

CREATE TABLE contacts_segments (
    id_contact INTEGER NOT NULL REFERENCES contacts(id_contact) ON DELETE CASCADE,
    id_segment INTEGER NOT NULL REFERENCES segments(id_segment) ON DELETE CASCADE,
    date_attribution TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (id_contact, id_segment)
);

CREATE TABLE campagnes_brevo (
    id_campagne SERIAL PRIMARY KEY,
    id_segment INTEGER REFERENCES segments(id_segment),
    nom VARCHAR(150) NOT NULL,
    objectif TEXT,
    date_preparation TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    statut VARCHAR(50) NOT NULL DEFAULT 'brouillon',
    id_brevo_campaign VARCHAR(100)
);

CREATE TABLE synchronisations_weezevent (
    id_synchronisation SERIAL PRIMARY KEY,
    date_debut TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    date_fin TIMESTAMP,
    statut VARCHAR(50) NOT NULL DEFAULT 'en_cours',
    nb_lignes_traitees INTEGER NOT NULL DEFAULT 0,
    nb_contacts_crees INTEGER NOT NULL DEFAULT 0,
    nb_contacts_mis_a_jour INTEGER NOT NULL DEFAULT 0,
    nb_erreurs INTEGER NOT NULL DEFAULT 0,
    message_erreur TEXT
);
```

Attention : ce script est un point de départ. Il ne dispense pas de produire le MCD, le MLD et les règles de gestion.

## 12. Données de test utiles

Pour que le backend puisse travailler, vous devez préparer des données de test.

Exemple :

```sql
INSERT INTO roles (nom, description) VALUES
('admin', 'Accès complet au dashboard'),
('gestionnaire', 'Accès limité aux fonctions de consultation et de synchronisation');

INSERT INTO segments (nom, description, type_segment) VALUES
('Nouveaux visiteurs', 'Contacts ayant une seule participation connue', 'automatique'),
('Supporters réguliers', 'Contacts ayant plusieurs participations connues', 'automatique'),
('Abonnés potentiels', 'Contacts à fort potentiel de fidélisation', 'automatique'),
('Partenaires', 'Contacts liés aux partenaires ou prospects BtoB', 'manuel');

INSERT INTO contacts (nom, prenom, email, telephone, source_origine, consentement_marketing) VALUES
('Martin', 'Alice', 'alice.martin@example.com', '0600000001', 'weezevent', TRUE),
('Durand', 'Sophie', 'sophie.durand@example.com', '0600000002', 'weezevent', TRUE),
('Benali', 'Karim', 'karim.benali@example.com', '0600000003', 'brevo', FALSE),
('Robert', 'Lucas', 'lucas.robert@example.com', NULL, 'manuel', NULL);

INSERT INTO evenements (nom, date_evenement, lieu, type_evenement, saison, id_weezevent) VALUES
('Nice Volley Ball vs Paris Volley', '2026-01-12 20:00:00', 'Salle Palmeira', 'match', '2025-2026', 'WZ_EVT_001'),
('Nice Volley Ball vs Montpellier', '2026-02-02 20:00:00', 'Salle Palmeira', 'match', '2025-2026', 'WZ_EVT_002');

INSERT INTO achats_billets (id_contact, id_evenement, date_achat, quantite, montant_total, type_tarif, code_promo, id_weezevent_order) VALUES
(1, 1, '2026-01-05 14:32:00', 2, 24.00, 'Famille', NULL, 'WZ_ORD_001'),
(2, 1, '2026-01-06 09:12:00', 1, 12.00, 'Plein tarif', NULL, 'WZ_ORD_002'),
(1, 2, '2026-01-25 18:02:00', 2, 24.00, 'Famille', 'RETOUR10', 'WZ_ORD_003');
```

Ces données permettent déjà de tester une fiche contact, un historique d’achats et quelques statistiques simples.

## 13. Exemple de CSV Weezevent simulé

Même si l’objectif est d’utiliser l’API Weezevent, il est utile d’avoir un CSV simulé pour tester rapidement le backend.

Exemple :

```csv
order_id,event_id,event_name,event_date,first_name,last_name,email,phone,ticket_type,quantity,total_amount,promo_code,purchase_date
WZ_ORD_001,WZ_EVT_001,Nice Volley Ball vs Paris Volley,2026-01-12 20:00:00,Alice,Martin,alice.martin@example.com,0600000001,Famille,2,24.00,,2026-01-05 14:32:00
WZ_ORD_002,WZ_EVT_001,Nice Volley Ball vs Paris Volley,2026-01-12 20:00:00,Sophie,Durand,sophie.durand@example.com,0600000002,Plein tarif,1,12.00,,2026-01-06 09:12:00
WZ_ORD_003,WZ_EVT_002,Nice Volley Ball vs Montpellier,2026-02-02 20:00:00,Alice,Martin,alice.martin@example.com,0600000001,Famille,2,24.00,RETOUR10,2026-01-25 18:02:00
WZ_ORD_004,WZ_EVT_002,Nice Volley Ball vs Montpellier,2026-02-02 20:00:00,Karim,Benali,karim.benali@example.com,0600000003,Etudiant,1,8.00,ETU2026,2026-01-27 11:45:00
```

Ce CSV doit permettre de tester :

* la création de contacts ;
* la détection d’un contact déjà existant ;
* la création d’événements ;
* la création d’achats ;
* le calcul du nombre d’achats par contact ;
* les segments “nouveau visiteur” et “supporter régulier”.

## 14. Segments possibles pour le prototype

Les segments doivent être simples et vérifiables. Ne créez pas de segmentation trop complexe.

Exemples de segments automatiques :

Nouveaux visiteurs : contacts ayant exactement un achat de billet.

Supporters réguliers : contacts ayant au moins deux achats de billets.

Abonnés potentiels : contacts ayant au moins deux achats, un consentement marketing positif et n’étant pas encore identifiés comme abonnés.

Contacts inactifs : contacts n’ayant pas eu d’achat récent. Pour le prototype, cette règle peut être simplifiée.

Contacts issus de Weezevent : contacts dont la source d’origine est Weezevent.

Exemple de requête SQL pour les supporters réguliers :

```sql
SELECT c.id_contact, c.nom, c.prenom, c.email, COUNT(a.id_achat) AS nb_achats
FROM contacts c
JOIN achats_billets a ON a.id_contact = c.id_contact
GROUP BY c.id_contact, c.nom, c.prenom, c.email
HAVING COUNT(a.id_achat) >= 2;
```

Exemple de requête SQL pour les nouveaux visiteurs :

```sql
SELECT c.id_contact, c.nom, c.prenom, c.email, COUNT(a.id_achat) AS nb_achats
FROM contacts c
JOIN achats_billets a ON a.id_contact = c.id_contact
GROUP BY c.id_contact, c.nom, c.prenom, c.email
HAVING COUNT(a.id_achat) = 1;
```

## 15. Indicateurs dashboard à prévoir

Le modèle de données doit permettre de produire quelques indicateurs simples.

Indicateurs prioritaires :

* nombre total de contacts ;
* nombre de contacts issus de Weezevent ;
* nombre de contacts avec consentement marketing ;
* nombre total d’événements ;
* nombre total d’achats ;
* chiffre d’affaires billetterie connu ;
* nombre de nouveaux visiteurs ;
* nombre de supporters réguliers ;
* nombre d’abonnés potentiels ;
* date de la dernière synchronisation Weezevent ;
* nombre d’erreurs lors de la dernière synchronisation.

Exemple de requête pour le nombre total de contacts :

```sql
SELECT COUNT(*) AS total_contacts
FROM contacts;
```

Exemple de requête pour le chiffre d’affaires total connu :

```sql
SELECT COALESCE(SUM(montant_total), 0) AS chiffre_affaires
FROM achats_billets;
```

Exemple de requête pour la dernière synchronisation :

```sql
SELECT *
FROM synchronisations_weezevent
ORDER BY date_debut DESC
LIMIT 1;
```

## 16. Questions importantes à trancher

Vous devez répondre à ces questions ou les transmettre au binôme projet si elles ne peuvent pas être tranchées immédiatement.

L’email est-il obligatoire pour créer un contact ?

Que faire si deux contacts ont le même email ?

Que faire si un contact n’a pas de téléphone ?

Que faire si Weezevent fournit un achat sans email ?

Est-ce qu’un achat peut contenir plusieurs billets ?

Faut-il stocker chaque billet séparément ou seulement l’achat global ?

Un événement correspond-il toujours à un match ?

Un contact peut-il être à la fois supporter régulier et partenaire ?

Le consentement marketing vient-il de Brevo, de Weezevent ou d’un formulaire ?

Une campagne Brevo cible-t-elle un seul segment ou plusieurs segments ?

Faut-il conserver l’historique de toutes les synchronisations ?

Que faire si une synchronisation échoue partiellement ?

Ces questions ne sont pas des détails. Elles peuvent changer le modèle.

## 17. Erreurs classiques à éviter

Ne mettez pas tous les champs dans une seule table `contacts`. C’est l’erreur la plus fréquente. Un contact peut avoir plusieurs achats, plusieurs segments, plusieurs interactions. Si tout est stocké dans une seule table, le modèle devient vite inutilisable.

Ne confondez pas un utilisateur et un contact. L’utilisateur est une personne qui utilise le dashboard. Le contact est une personne suivie par le club.

Ne confondez pas un événement et un achat. Un événement peut générer plusieurs achats. Un achat concerne un événement.

Ne stockez pas une liste de segments dans un champ texte du contact. Par exemple, éviter `segments = "famille,supporter,partenaire"`. Il faut une table d’association.

Ne créez pas une entité pour chaque type de contact. Il ne faut pas faire une table `Famille`, une table `Supporter`, une table `Partenaire`, une table `Licencie`. Un contact peut avoir plusieurs statuts. Les segments ou types permettent de gérer cela plus proprement.

Ne stockez pas les mots de passe en clair. Un mot de passe doit toujours être hashé.

Ne rendez pas toutes les données obligatoires. Dans la réalité, les exports externes peuvent être incomplets. Le modèle doit accepter certains champs vides.

Ne négligez pas les identifiants externes. Les identifiants Weezevent et Brevo sont utiles pour éviter les doublons et faciliter les synchronisations.

Ne supprimez pas brutalement les données liées. Si vous utilisez `ON DELETE CASCADE`, vous devez comprendre ses conséquences. Supprimer un contact peut supprimer ses achats. Est-ce souhaitable ? À discuter.

## 18. Note RGPD simplifiée

Le dashboard manipulera des données personnelles : nom, prénom, email, téléphone, historique d’achats, participation à des événements, segments marketing. Il faut donc appliquer quelques principes simples.

Le principe de minimisation signifie qu’il ne faut stocker que les données nécessaires. Si une donnée n’est pas utile au projet, elle ne doit pas être collectée.

Le principe de finalité signifie que les données doivent être utilisées pour un objectif clair : suivi des contacts, segmentation, communication marketing, pilotage de la billetterie. Il ne faut pas réutiliser les données pour un objectif sans rapport.

Le consentement marketing doit être pris en compte. Un contact sans consentement clair ne doit pas être synchronisé vers une campagne marketing active.

Les droits d’accès doivent être limités. Tous les utilisateurs internes ne doivent pas avoir les mêmes droits. Un utilisateur restreint ne doit pas pouvoir exporter toute la base sans contrôle.

Les données sensibles ou inutiles doivent être évitées. Il ne faut pas stocker d’informations personnelles excessives, de commentaires subjectifs ou de données non nécessaires.

Les mots de passe doivent être hashés. Les clés API Weezevent et Brevo ne doivent jamais être stockées dans la base ni dans le code source. Elles doivent être placées dans un fichier `.env` non versionné.

## 19. Travail attendu pendant la première demi-journée

Pendant la première demi-journée, le pôle MERISE doit impérativement produire une première version exploitable.

Objectif minimal avant midi :

* liste des entités ;
* règles de gestion principales ;
* premier MCD ;
* premières cardinalités ;
* questions bloquantes ;
* début du dictionnaire de données.

Il vaut mieux produire une V1 imparfaite mais exploitable qu’attendre trop longtemps pour produire une version parfaite. Le backend a besoin de vous rapidement.

## 20. Travail attendu en fin de journée

En fin de journée, vous devez fournir :

* MCD V1 propre ;
* MLD V1 ;
* script SQL V1 ;
* données de test ;
* CSV Weezevent simulé ;
* note RGPD courte ;
* liste des points à améliorer.

Ces documents doivent être déposés dans l’espace projet et présentés rapidement aux autres pôles.

## 21. Checklist finale du pôle MERISE

Avant de considérer votre travail comme terminé, vérifiez les points suivants :

```text
[ ] Les entités principales sont identifiées.
[ ] Les règles de gestion sont rédigées.
[ ] Les cardinalités sont justifiées.
[ ] Le MCD est lisible.
[ ] Le MLD correspond au MCD.
[ ] Les clés primaires sont définies.
[ ] Les clés étrangères sont définies.
[ ] Les relations plusieurs-à-plusieurs sont transformées en tables d’association.
[ ] Les données personnelles sont identifiées.
[ ] Le consentement marketing est pris en compte.
[ ] Le script SQL peut être exécuté dans PostgreSQL.
[ ] Des données de test existent.
[ ] Un CSV Weezevent simulé existe.
[ ] Le backend peut commencer à travailler à partir de votre modèle.
```

## 22. Objectif pédagogique

Ce travail doit vous permettre de comprendre pourquoi la modélisation des données est une étape centrale dans un projet applicatif. Un bon modèle de données permet de développer plus vite, de limiter les erreurs, d’éviter les doublons, de produire des indicateurs fiables et de connecter plus facilement plusieurs systèmes.

Dans ce projet, MERISE n’est pas un exercice scolaire séparé du développement. C’est la base du dashboard. Si le modèle est solide, l’API Slim, le frontend JavaScript, les synchronisations Brevo / Weezevent et le déploiement seront beaucoup plus simples à construire.
