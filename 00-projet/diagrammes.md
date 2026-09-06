# Diagrammes Mermaid

Ces diagrammes sont volontairement simples. Ils doivent aider les étudiants SLAM et SISR à partager une même représentation du projet.

## Architecture cible

```mermaid
flowchart TD
    U[Utilisateur club] -->|HTTPS| RP[Nginx / Reverse proxy]
    RP --> F[Frontend HTML / Tailwind / JS]
    RP --> A[API PHP Slim]
    A --> DB[(PostgreSQL)]
    A --> W[API Weezevent]
    A --> B[API Brevo]
    DB --> BK[Sauvegardes]
    A --> L[Logs applicatifs]
```

## Flux Weezevent vers dashboard

```mermaid
sequenceDiagram
    actor Gestionnaire
    participant Front as Frontend
    participant API as API Slim
    participant W as Weezevent
    participant DB as PostgreSQL

    Gestionnaire->>Front: Lancer la synchronisation
    Front->>API: POST /sync/weezevent
    API->>W: Récupérer événements, billets, invitations
    W-->>API: Données billetterie
    API->>API: Nettoyer et transformer
    API->>DB: Insérer ou mettre à jour
    DB-->>API: Résultat persistant
    API-->>Front: Bilan de synchronisation
    Front-->>Gestionnaire: Résumé affiché
```

## Flux événement vers export contacts

```mermaid
sequenceDiagram
    actor Gestionnaire
    participant Front as Interface
    participant API as API Slim
    participant DB as PostgreSQL

    Gestionnaire->>Front: Sélectionner un événement
    Front->>API: GET /events/{id}/contacts
    API->>DB: Chercher contacts liés à l'événement
    DB-->>API: Contacts et statuts
    API-->>Front: Liste affichable
    Gestionnaire->>Front: Demander export
    Front->>API: GET /events/{id}/contacts/export
    API-->>Front: CSV ou fichier généré
```

## Flux Brevo avec contrôle du consentement

```mermaid
sequenceDiagram
    actor Gestionnaire
    participant Front as Frontend
    participant API as API Slim
    participant DB as PostgreSQL
    participant B as Brevo

    Gestionnaire->>Front: Synchroniser un segment
    Front->>API: POST /sync/brevo
    API->>DB: Charger les contacts du segment
    API->>API: Exclure désinscrits et non éligibles
    API->>B: Créer ou mettre à jour les contacts autorisés
    B-->>API: Résultat Brevo
    API->>DB: Enregistrer le bilan
    API-->>Front: Contacts envoyés, ignorés, erreurs
```

## Cycle consentement / désinscription

```mermaid
stateDiagram-v2
    [*] --> ConsentementInconnu
    ConsentementInconnu --> ConsentementAccepte: consentement explicite
    ConsentementInconnu --> Desinscrit: refus ou désinscription
    ConsentementAccepte --> Desinscrit: désinscription
    Desinscrit --> ConsentementAccepte: réinscription explicite
    Desinscrit --> ExcluBrevo: tentative de synchronisation
    ExcluBrevo --> Desinscrit: exclusion conservée
```

## Pipeline de déploiement cible

```mermaid
flowchart LR
    Dev[Commit GitHub] --> CI[GitHub Actions]
    CI --> Tests[Tests et vérifications]
    Tests --> Build[Build ou préparation Docker]
    Build --> Deploy[Déploiement VPS]
    Deploy --> Smoke[Smoke tests]
    Smoke --> OK[Version livrée]
```

## Modèle de données simplifié

```mermaid
erDiagram
    CONTACT ||--o{ BILLET : possede
    EVENEMENT ||--o{ BILLET : concerne
    CONTACT }o--o{ SEGMENT : appartient
    SEGMENT ||--o{ CAMPAGNE_BREVO : cible
    SYNCHRONISATION_WEEZEVENT ||--o{ EVENEMENT : alimente
    SYNCHRONISATION_WEEZEVENT ||--o{ BILLET : importe
    UTILISATEUR }o--|| ROLE : possede

    CONTACT {
        int id_contact
        string email
        string nom
        string prenom
        string consentement
        string id_brevo
    }

    EVENEMENT {
        int id_evenement
        string nom
        date date_evenement
        string id_weezevent
    }

    BILLET {
        int id_billet
        string type_billet
        boolean invitation
        boolean present
        decimal montant
    }
```

## Usage attendu

Ces diagrammes peuvent être copiés dans les supports de présentation, dans la documentation technique ou dans les fiches individuelles BTS SIO. Ils doivent rester cohérents avec l'implémentation réelle : si le modèle ou les flux changent, ce fichier doit être mis à jour.
