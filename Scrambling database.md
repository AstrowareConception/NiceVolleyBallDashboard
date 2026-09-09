# TP RGPD / Cybersécurité — Dépersonnalisation d’une base de données

## Projet support : Nice Volley Ball Dashboard

## 1. Contexte du TP

Le projet Nice Volley Ball Dashboard manipule des données issues de services externes comme Weezevent et Brevo. Ces données permettent au club de suivre des événements, des billets, des contacts, des segments marketing, des consentements et des synchronisations.

Dans un environnement de développement, de test, de démonstration ou de formation, il est dangereux et généralement inutile de travailler avec des données personnelles réelles. Le projet doit donc prévoir un mécanisme permettant de transformer une base contenant des données réalistes en une base exploitable sans exposer les personnes concernées.

L’objectif de ce TP est de concevoir, implémenter et tester une procédure de dépersonnalisation de la base de données.

Le but n’est pas seulement de modifier quelques noms à la main. Le but est de raisonner comme un professionnel : identifier les données personnelles, évaluer les risques, choisir une stratégie de transformation, préserver l’intégrité de la base, vérifier que l’application fonctionne encore et documenter les limites de la solution.

## 2. Objectifs pédagogiques

À la fin du TP, vous devez être capables de :

* identifier les données personnelles dans un schéma relationnel ;
* distinguer donnée personnelle directe, donnée indirecte, donnée technique et donnée métier ;
* comprendre la différence entre anonymisation, pseudonymisation, masquage et génération de données fictives ;
* proposer une stratégie de dépersonnalisation adaptée à une base PostgreSQL ;
* modifier des données tout en respectant les contraintes d’intégrité ;
* préserver les relations nécessaires au fonctionnement de l’application ;
* éviter les fuites accidentelles de données réelles ;
* écrire un script SQL de scrambling ;
* tester le résultat ;
* documenter la démarche, les choix et les limites.

## 3. Vocabulaire essentiel

### Donnée personnelle

Une donnée personnelle est une information qui permet d’identifier directement ou indirectement une personne physique.

Exemples directs :

* nom ;
* prénom ;
* email ;
* téléphone ;
* identifiant de compte ;
* adresse postale ;
* photographie.

Exemples indirects :

* historique d’achats ;
* participation à un événement ;
* combinaison date + événement + tarif ;
* identifiant externe Weezevent ;
* appartenance à un segment très précis ;
* date de consentement ;
* comportement marketing.

Dans le projet Nice Volley Ball, un contact peut être identifié directement par son nom, son prénom, son email ou son téléphone. Il peut aussi être identifié indirectement par son historique de billets, ses événements, ses invitations ou ses segments.

### Anonymisation

L’anonymisation vise à rendre impossible, en pratique, la ré-identification d’une personne. Si l’anonymisation est réellement effective, les données ne sont plus considérées comme personnelles.

Attention : c’est difficile à garantir. Si une personne peut être retrouvée par recoupement avec d’autres informations, l’anonymisation n’est pas suffisante.

### Pseudonymisation

La pseudonymisation remplace les identifiants directs par des identifiants artificiels ou des valeurs transformées. Elle réduit les risques, mais elle ne supprime pas forcément le caractère personnel des données.

Exemple :

```text
alice.martin@gmail.com → contact_00042@example.invalid
```

Si quelqu’un conserve une table de correspondance entre `contact_00042` et Alice Martin, alors la ré-identification reste possible.

### Masquage

Le masquage consiste à remplacer, cacher ou tronquer une donnée.

Exemples :

```text
0601020304 → 06******04
alice.martin@gmail.com → a***@gmail.com
```

Le masquage est utile pour l’affichage ou les logs, mais il n’est pas toujours suffisant pour produire une base de test.

### Scrambling

Le scrambling consiste à mélanger ou transformer les données pour casser le lien avec les personnes réelles.

Exemples :

* mélanger les prénoms entre contacts ;
* remplacer les emails par des emails fictifs ;
* remplacer les téléphones par de faux numéros ;
* générer de nouveaux noms ;
* modifier les dates ;
* remplacer les identifiants externes.

Attention : un simple mélange de noms et prénoms n’est pas suffisant si les emails ou les téléphones restent réels.

### Données synthétiques

Les données synthétiques sont entièrement générées. Elles ressemblent à des données réelles, mais ne correspondent à aucune personne réelle.

Pour une base de démonstration, c’est souvent la meilleure approche : on conserve la structure et les relations, mais on remplace toutes les informations personnelles par des valeurs fictives.

## 4. Analyse du schéma de la base Nice Volley Ball

Le script `init_database.sql` contient les principales tables suivantes :

```text
administrateur
contact
consentement
segment
evenement
billet
consentement_contact
contact_segment
contact_billet
billet_evenement
```

La base repose sur un modèle logique cohérent :

* des administrateurs utilisent la plateforme ;
* des contacts représentent les personnes connues du club ;
* des événements proviennent de Weezevent ;
* des billets sont associés à des contacts et à des événements ;
* des consentements sont associés à des contacts ;
* des segments permettent de regrouper les contacts ;
* des tables de liaison préservent les relations plusieurs-à-plusieurs.

Le point central du TP est de conserver les relations utiles tout en supprimant ou transformant les données permettant d’identifier les personnes.

## 5. Classification des données à traiter

### Table `administrateur`

Colonnes principales :

```text
idAdministrateur
nom
prenom
email
mot_de_passe
statut
```

Analyse :

| Colonne          | Nature                     | Risque         | Traitement recommandé         |
| ---------------- | -------------------------- | -------------- | ----------------------------- |
| idAdministrateur | identifiant interne        | faible à moyen | conserver                     |
| nom              | donnée personnelle directe | élevé          | remplacer                     |
| prenom           | donnée personnelle directe | élevé          | remplacer                     |
| email            | donnée personnelle directe | élevé          | remplacer                     |
| mot_de_passe     | donnée d’authentification  | très élevé     | remplacer par un hash de test |
| statut           | donnée fonctionnelle       | faible         | conserver                     |

Point critique : même si le mot de passe est hashé, un hash réel ne doit pas être diffusé dans une base de test ou une image Docker publique. Il faut le remplacer par un hash généré pour un mot de passe de démonstration.

### Table `contact`

Colonnes principales :

```text
idContact
nom
prenom
email
phone
source
date_creation
date_derniere_maj
```

Analyse :

| Colonne           | Nature                     | Risque         | Traitement recommandé      |
| ----------------- | -------------------------- | -------------- | -------------------------- |
| idContact         | identifiant interne        | moyen          | conserver si non exposé    |
| nom               | donnée personnelle directe | élevé          | remplacer                  |
| prenom            | donnée personnelle directe | élevé          | remplacer                  |
| email             | donnée personnelle directe | très élevé     | remplacer par email fictif |
| phone             | donnée personnelle directe | élevé          | remplacer ou mettre à NULL |
| source            | donnée métier              | faible à moyen | conserver ou généraliser   |
| date_creation     | quasi-identifiant          | moyen          | tronquer ou décaler        |
| date_derniere_maj | quasi-identifiant          | moyen          | tronquer ou décaler        |

Point critique : l’email est souvent la clé de dédoublonnage. Il faut donc générer des emails fictifs uniques, mais conserver leur unicité.

### Table `consentement`

Colonnes principales :

```text
idConsentement
date
source
statut
```

Analyse :

| Colonne        | Nature                                 | Risque                    | Traitement recommandé  |
| -------------- | -------------------------------------- | ------------------------- | ---------------------- |
| idConsentement | identifiant interne                    | faible                    | conserver              |
| date           | donnée comportementale liée au contact | moyen                     | tronquer ou décaler    |
| source         | origine du consentement                | faible à moyen            | conserver si générique |
| statut         | état du consentement                   | personnel mais nécessaire | conserver              |

Point important : le statut de consentement doit être conservé, car il est nécessaire pour tester l’exclusion des contacts désinscrits lors de l’envoi vers Brevo.

### Table `segment`

Colonnes principales :

```text
idSegment
nom_segment
date_creation
brevo_id
```

Analyse :

| Colonne       | Nature              | Risque         | Traitement recommandé      |
| ------------- | ------------------- | -------------- | -------------------------- |
| idSegment     | identifiant interne | faible         | conserver                  |
| nom_segment   | donnée métier       | faible à moyen | conserver ou généraliser   |
| date_creation | donnée technique    | faible         | conserver ou tronquer      |
| brevo_id      | identifiant externe | moyen          | remplacer ou mettre à NULL |

Un segment n’identifie pas directement une personne, mais un segment très précis peut devenir sensible si peu de personnes y appartiennent.

### Table `evenement`

Colonnes principales :

```text
idEvenementWeezevent
nom_evenement
date
lieu
type
saison
```

Analyse :

| Colonne              | Nature                | Risque                                  | Traitement recommandé    |
| -------------------- | --------------------- | --------------------------------------- | ------------------------ |
| idEvenementWeezevent | identifiant externe   | faible à moyen                          | conserver ou remplacer   |
| nom_evenement        | donnée métier         | faible                                  | conserver si public      |
| date                 | donnée événementielle | faible seule, moyenne liée aux contacts | conserver ou généraliser |
| lieu                 | donnée métier         | faible                                  | conserver ou généraliser |
| type                 | donnée métier         | faible                                  | conserver                |
| saison               | donnée métier         | faible                                  | conserver                |

Les événements ne sont pas forcément des données personnelles. En revanche, la présence d’une personne à un événement est une donnée personnelle. La relation entre `contact`, `billet` et `evenement` doit donc être traitée avec prudence.

### Table `billet`

Colonnes principales :

```text
idBilletWeezevent
date_achat
quantite
montant_total
type_tarif
code_promotionnel
origine
```

Analyse :

| Colonne           | Nature                  | Risque         | Traitement recommandé             |
| ----------------- | ----------------------- | -------------- | --------------------------------- |
| idBilletWeezevent | identifiant externe     | moyen          | remplacer si base publiée         |
| date_achat        | quasi-identifiant       | moyen          | tronquer ou décaler               |
| quantite          | donnée métier           | faible         | conserver                         |
| montant_total     | donnée transactionnelle | moyen          | conserver, généraliser ou bruiter |
| type_tarif        | donnée métier           | faible à moyen | conserver                         |
| code_promotionnel | donnée marketing        | faible à moyen | remplacer si sensible             |
| origine           | donnée métier           | faible         | conserver                         |

Le billet devient personnel lorsqu’il est relié à un contact. Il faut donc faire attention aux tables de liaison.

### Tables de liaison

```text
consentement_contact
contact_segment
contact_billet
billet_evenement
```

Ces tables ne contiennent pas directement de nom ou d’email, mais elles relient les personnes à des événements, des billets, des consentements ou des segments.

Recommandation : conserver les tables de liaison pour préserver le fonctionnement de l’application, mais remplacer les informations personnelles dans les tables liées.

## 6. Stratégie recommandée pour le projet

Pour le projet Nice Volley Ball, la meilleure stratégie pédagogique est la suivante :

1. travailler uniquement sur une copie de la base ;
2. sauvegarder la base avant modification ;
3. remplacer les administrateurs réels par des administrateurs de test ;
4. remplacer les noms, prénoms, emails et téléphones des contacts ;
5. conserver les relations entre contacts, billets, événements, segments et consentements ;
6. conserver les statuts de consentement pour tester Brevo ;
7. tronquer ou décaler les dates trop précises ;
8. remplacer les identifiants externes sensibles si la base doit être publiée ;
9. vérifier que l’application fonctionne encore ;
10. documenter les risques résiduels.

L’objectif est d’obtenir une base utilisable pour :

* les tests applicatifs ;
* la démonstration ;
* la documentation ;
* la passation aux SISR ;
* une image Docker de démonstration ;
* une recette sans exposition de données réelles.

## 7. Techniques à mettre en œuvre

### Technique 1 — Remplacement par valeurs fictives

C’est la technique la plus simple et la plus adaptée aux noms, prénoms, emails et téléphones.

Exemple :

```text
Alice Martin → Camille Bernard
alice.martin@gmail.com → nvb.contact.42@example.invalid
0601020304 → +33610000042
```

Avantage : facile à comprendre et à vérifier.

Limite : les données deviennent moins réalistes si les valeurs sont trop artificielles.

### Technique 2 — Génération déterministe

La génération déterministe produit toujours la même valeur fictive pour le même identifiant.

Exemple :

```text
idContact = 42 → nvb.contact.42@example.invalid
```

Avantage : stable, reproductible, facile à tester.

Limite : si l’identifiant interne est lui-même exposé, on peut suivre une personne fictive dans toute la base. Ce n’est pas forcément grave pour une base de test, mais il faut le savoir.

### Technique 3 — Mélange de colonnes

Le mélange consiste à permuter les valeurs d’une colonne entre plusieurs lignes.

Exemple :

```text
Les prénoms sont mélangés entre les contacts.
Les noms sont mélangés entre les contacts.
```

Avantage : conserve une distribution réaliste.

Limite : les valeurs restent de vraies valeurs. Si les prénoms, noms ou emails appartiennent à de vraies personnes, le risque n’est pas supprimé. Pour un vrai jeu de démonstration, il vaut mieux générer de fausses valeurs.

### Technique 4 — Généralisation

La généralisation réduit la précision d’une donnée.

Exemples :

```text
2026-09-09 20:14:23 → 2026-09-09
12,50 € → tranche 10-20 €
0601020304 → téléphone présent : oui
```

Avantage : conserve une information statistique utile.

Limite : la donnée peut rester ré-identifiante si elle est croisée avec d’autres informations.

### Technique 5 — Suppression

La suppression consiste à mettre une donnée à `NULL` ou à la supprimer lorsqu’elle n’est pas nécessaire.

Exemple :

```sql
UPDATE contact SET phone = NULL;
```

Avantage : très efficace si la donnée n’est pas utile.

Limite : peut empêcher de tester certaines fonctionnalités.

### Technique 6 — Remplacement des identifiants externes

Les identifiants Weezevent ou Brevo peuvent permettre de recroiser la base avec des systèmes externes. Dans une base destinée à circuler hors de l’environnement du client, ils doivent être supprimés ou remplacés.

Exemples :

```text
idBilletWeezevent réel → 900000001
brevo_id réel → NULL
```

Avantage : réduit le risque de réconciliation avec les services externes.

Limite : l’application ne pourra plus synchroniser cette base avec les vrais services externes.

### Technique 7 — Traitement des mots de passe

Les mots de passe ne doivent jamais être conservés. Même hashés, ils doivent être remplacés dans une base de test.

Méthode recommandée :

```bash
php -r "echo password_hash('Test1234!', PASSWORD_BCRYPT), PHP_EOL;"
```

Puis remplacer tous les hashes administrateurs par un hash de test.

## 8. Script SQL de scrambling — version de départ

Ce script est un point de départ. Il doit être exécuté uniquement sur une base de test ou une copie de la base.

```sql
BEGIN;

-- =============================================================
-- 1. Administrateurs : remplacement complet
-- =============================================================

-- Remplacer <HASH_BCRYPT_TEST> par un hash généré avec :
-- php -r "echo password_hash('Test1234!', PASSWORD_BCRYPT), PHP_EOL;"

UPDATE administrateur
SET
    nom = 'AdminTest' || idAdministrateur,
    prenom = 'NVB',
    email = 'admin.' || idAdministrateur || '@example.invalid',
    mot_de_passe = '<HASH_BCRYPT_TEST>',
    statut = 'actif';

-- =============================================================
-- 2. Contacts : noms, prénoms, emails, téléphones
-- =============================================================

WITH params AS (
    SELECT
        ARRAY[
            'Martin', 'Bernard', 'Dubois', 'Robert', 'Richard',
            'Petit', 'Durand', 'Leroy', 'Moreau', 'Simon',
            'Laurent', 'Lefebvre', 'Michel', 'Garcia', 'David'
        ] AS noms,
        ARRAY[
            'Camille', 'Lucas', 'Manon', 'Nolan', 'Ines',
            'Hugo', 'Emma', 'Noah', 'Lina', 'Adam',
            'Sarah', 'Jules', 'Louise', 'Nathan', 'Chloe'
        ] AS prenoms
),
mapping AS (
    SELECT
        c.idContact,
        row_number() OVER (ORDER BY c.idContact) AS rn,
        p.noms,
        p.prenoms
    FROM contact c
    CROSS JOIN params p
)
UPDATE contact c
SET
    nom = m.noms[((m.rn - 1) % array_length(m.noms, 1)) + 1] || '_' || c.idContact,
    prenom = m.prenoms[((m.rn - 1) % array_length(m.prenoms, 1)) + 1],
    email = 'nvb.contact.' || c.idContact || '@example.invalid',
    phone = CASE
        WHEN c.phone IS NULL THEN NULL
        ELSE '+336' || lpad((10000000 + c.idContact)::text, 8, '0')
    END,
    date_creation = date_trunc('day', c.date_creation),
    date_derniere_maj = CASE
        WHEN c.date_derniere_maj IS NULL THEN NULL
        ELSE date_trunc('day', c.date_derniere_maj)
    END
FROM mapping m
WHERE c.idContact = m.idContact;

-- =============================================================
-- 3. Consentements : conserver le statut, réduire la précision
-- =============================================================

UPDATE consentement
SET
    date = date_trunc('day', date),
    source = CASE
        WHEN source ILIKE '%brevo%' THEN 'API_Brevo_test'
        WHEN source ILIKE '%weezevent%' THEN 'API_Weezevent_test'
        WHEN source ILIKE '%formulaire%' THEN 'formulaire_test'
        ELSE 'source_test'
    END;

-- =============================================================
-- 4. Segments : supprimer l’identifiant Brevo réel
-- =============================================================

UPDATE segment
SET
    brevo_id = NULL,
    date_creation = date_trunc('day', date_creation);

-- =============================================================
-- 5. Événements : option pédagogique
-- =============================================================

-- Si les événements sont publics, on peut conserver nom/date/lieu.
-- Si la base doit devenir totalement démonstrative, on peut généraliser.

UPDATE evenement
SET
    nom_evenement = 'Événement test #' || idEvenementWeezevent,
    lieu = 'Salle de démonstration',
    type = 'match',
    date = date_trunc('day', date),
    saison = COALESCE(saison, 'saison-test');

-- =============================================================
-- 6. Billets : réduire les risques sans casser les statistiques
-- =============================================================

UPDATE billet
SET
    date_achat = date_trunc('day', date_achat) + interval '12 hours',
    code_promotionnel = CASE
        WHEN code_promotionnel IS NULL THEN NULL
        ELSE 'PROMO_TEST_' || idBilletWeezevent
    END,
    origine = CASE
        WHEN origine ILIKE '%weezevent%' THEN 'API_Weezevent_test'
        WHEN origine ILIKE '%web%' THEN 'widget_web_test'
        ELSE 'origine_test'
    END;

COMMIT;
```

## 9. Variante avancée — remplacer les identifiants externes Weezevent

Cette étape est plus sensible, car `idBilletWeezevent` et `idEvenementWeezevent` sont utilisés comme clés primaires et clés étrangères. Grâce aux contraintes `ON UPDATE CASCADE`, les tables de liaison doivent suivre la modification.

À faire uniquement après sauvegarde et sur une copie.

```sql
BEGIN;

-- Remappage des billets Weezevent
WITH mapping_billet AS (
    SELECT
        idBilletWeezevent AS old_id,
        900000000 + row_number() OVER (ORDER BY idBilletWeezevent) AS new_id
    FROM billet
)
UPDATE billet b
SET idBilletWeezevent = m.new_id
FROM mapping_billet m
WHERE b.idBilletWeezevent = m.old_id;

-- Remappage des événements Weezevent
WITH mapping_evenement AS (
    SELECT
        idEvenementWeezevent AS old_id,
        800000000 + row_number() OVER (ORDER BY idEvenementWeezevent) AS new_id
    FROM evenement
)
UPDATE evenement e
SET idEvenementWeezevent = m.new_id
FROM mapping_evenement m
WHERE e.idEvenementWeezevent = m.old_id;

COMMIT;
```

Attention : après cette étape, la base ne peut plus être utilisée pour resynchroniser correctement avec les vrais identifiants Weezevent. C’est normal pour une base de démonstration, mais il faut le documenter.

## 10. Tests de validation

Après le scrambling, il faut prouver que la base est utilisable et que les données personnelles directes ont disparu.

### Vérifier qu’il ne reste pas d’emails réels

```sql
SELECT COUNT(*) AS emails_non_fictifs
FROM contact
WHERE email NOT LIKE '%@example.invalid';
```

Résultat attendu :

```text
0
```

Même test pour les administrateurs :

```sql
SELECT COUNT(*) AS admins_non_fictifs
FROM administrateur
WHERE email NOT LIKE '%@example.invalid';
```

Résultat attendu :

```text
0
```

### Vérifier que les téléphones sont fictifs ou absents

```sql
SELECT COUNT(*) AS telephones_suspects
FROM contact
WHERE phone IS NOT NULL
  AND phone NOT LIKE '+336%';
```

Résultat attendu :

```text
0
```

### Vérifier que les relations sont conservées

```sql
SELECT COUNT(*) AS liens_contact_billet
FROM contact_billet;
```

Le nombre doit rester cohérent avec l’état avant scrambling.

### Vérifier l’absence d’orphelins

```sql
SELECT COUNT(*) AS billets_sans_contact_valide
FROM contact_billet cb
LEFT JOIN contact c ON c.idContact = cb.idContact
WHERE c.idContact IS NULL;
```

Résultat attendu :

```text
0
```

```sql
SELECT COUNT(*) AS billets_sans_evenement_valide
FROM billet_evenement be
LEFT JOIN evenement e ON e.idEvenementWeezevent = be.idEvenementWeezevent
WHERE e.idEvenementWeezevent IS NULL;
```

Résultat attendu :

```text
0
```

### Vérifier que les fonctionnalités métier restent testables

Les étudiants doivent vérifier dans l’application :

* affichage de la liste des contacts ;
* affichage des événements ;
* affichage des billets liés à un événement ;
* extraction des nouveaux spectateurs ;
* export des contacts d’un événement ;
* exclusion des contacts non consentants ;
* synchronisation Brevo en mode test ou désactivée ;
* absence d’envoi vers de vrais emails.

## 11. Déroulé proposé du TP

### Partie 1 — Analyse du schéma

Durée recommandée : 45 minutes.

Travail attendu :

* lire le script SQL ;
* identifier les tables ;
* repérer les clés primaires et étrangères ;
* classer les colonnes selon leur niveau de risque ;
* distinguer données personnelles directes et indirectes ;
* remplir une matrice de traitement.

Matrice à compléter :

| Table          | Colonne      | Type de donnée            | Risque     | Traitement proposé       |
| -------------- | ------------ | ------------------------- | ---------- | ------------------------ |
| contact        | email        | identifiant direct        | élevé      | email fictif unique      |
| contact        | phone        | identifiant direct        | élevé      | téléphone fictif ou NULL |
| billet         | date_achat   | quasi-identifiant         | moyen      | troncature à la journée  |
| consentement   | statut       | donnée personnelle utile  | moyen      | conserver                |
| administrateur | mot_de_passe | secret / authentification | très élevé | hash de test             |

### Partie 2 — Choix de stratégie

Durée recommandée : 30 minutes.

Travail attendu :

* décider ce qui doit être remplacé ;
* décider ce qui peut être conservé ;
* décider ce qui doit être supprimé ;
* justifier les choix.

Questions à traiter :

* faut-il conserver les événements réels ?
* faut-il conserver les montants ?
* faut-il conserver les dates précises ?
* faut-il conserver les identifiants Weezevent ?
* comment éviter d’envoyer de vrais emails ?
* quelles données sont nécessaires pour tester les fonctionnalités ?

### Partie 3 — Écriture du script SQL

Durée recommandée : 1 h 30.

Travail attendu :

* créer un fichier `scramble_database.sql` ;
* écrire les `UPDATE` nécessaires ;
* travailler dans une transaction ;
* utiliser des valeurs fictives ;
* préserver les contraintes d’unicité ;
* préserver les relations entre tables.

Contraintes :

* ne pas casser les clés étrangères ;
* ne pas supprimer les données nécessaires au dashboard ;
* ne pas exposer les vrais emails ;
* ne pas conserver de vrais mots de passe ou hashes réels ;
* ne pas laisser de table de correspondance persistante dans la base.

### Partie 4 — Tests SQL et applicatifs

Durée recommandée : 1 h.

Travail attendu :

* exécuter les requêtes de contrôle ;
* vérifier l’absence d’emails réels ;
* vérifier les relations ;
* vérifier que l’application fonctionne ;
* documenter les anomalies.

Livrables :

* script SQL ;
* captures des tests ;
* résultat des requêtes ;
* rapport court de validation.

### Partie 5 — Documentation et bilan

Durée recommandée : 45 minutes.

Travail attendu :

* expliquer la méthode utilisée ;
* indiquer les données modifiées ;
* préciser les limites ;
* indiquer si la base est anonymisée, pseudonymisée ou simplement masquée ;
* proposer des améliorations.

## 12. Livrables attendus

Chaque groupe doit produire :

```text
scramble_database.sql
rapport-scrambling.md
captures-tests/
matrice-donnees-personnelles.md
```

Le rapport doit contenir :

* objectif du traitement ;
* tables analysées ;
* données personnelles identifiées ;
* stratégie retenue ;
* script ou extraits de script ;
* tests effectués ;
* résultats obtenus ;
* limites ;
* risques résiduels ;
* conclusion.

## 13. Critères d’évaluation

Le TP peut être évalué selon les critères suivants :

| Critère                    | Attendu                                                                  |
| -------------------------- | ------------------------------------------------------------------------ |
| Analyse du schéma          | Les tables et colonnes sensibles sont correctement identifiées           |
| Compréhension RGPD         | Les notions anonymisation / pseudonymisation / masquage sont distinguées |
| Qualité SQL                | Le script est lisible, transactionnel et respecte les contraintes        |
| Préservation fonctionnelle | L’application reste utilisable après scrambling                          |
| Sécurité                   | Aucun vrai email, téléphone, mot de passe ou secret ne reste exposé      |
| Tests                      | Les contrôles SQL et applicatifs sont réalisés                           |
| Documentation              | La démarche et les limites sont clairement expliquées                    |
| Recul critique             | Les risques résiduels sont identifiés                                    |

## 14. Points d’attention professionnels

Ne jamais exécuter un script de scrambling directement sur la base de production.

Toujours travailler sur une copie ou un dump restauré.

Toujours effectuer une sauvegarde avant transformation.

Ne jamais versionner un dump contenant de vraies données personnelles.

Ne jamais publier une image Docker contenant une base réelle.

Ne jamais laisser de clé Brevo ou Weezevent dans l’image Docker.

Ne jamais tester l’envoi Brevo avec de vrais contacts depuis une base de démonstration.

Ne jamais dire qu’une base est anonymisée si une ré-identification reste possible.

Toujours documenter les limites.

## 15. Conclusion

La dépersonnalisation d’une base n’est pas une simple opération technique. C’est un travail à la fois juridique, fonctionnel, SQL, applicatif et cybersécurité.

Dans le projet Nice Volley Ball Dashboard, l’objectif est de produire une base de démonstration réaliste, utile pour les tests et la livraison, mais qui ne révèle pas les données personnelles des spectateurs, contacts, invités ou administrateurs.

Le bon résultat n’est donc pas seulement un script qui modifie les noms. Le bon résultat est une base :

* sans données personnelles directes ;
* sans vrais emails ;
* sans vrais téléphones ;
* sans vrais mots de passe ou hashes réels ;
* avec des relations conservées ;
* avec des statistiques encore exploitables ;
* avec des fonctionnalités applicatives toujours testables ;
* avec une documentation claire des choix et des limites.
