# Aide complète — Découvrir Slim pour créer l’API du dashboard Nice Volley Ball

## 1. À quoi sert Slim dans notre projet ?

Slim est un micro-framework PHP. Cela signifie qu’il ne cherche pas à tout faire à votre place comme un framework complet de type Laravel ou Symfony. Son rôle principal est de vous aider à construire proprement une application web ou une API REST.

Dans le projet Nice Volley Ball Dashboard, Slim sera utilisé pour créer le backend de l’application. Le frontend sera développé en HTML, Tailwind CSS et JavaScript vanilla. Le JavaScript appellera l’API Slim avec `fetch()`. Slim recevra les requêtes, interrogera la base PostgreSQL, communiquera éventuellement avec les API Weezevent et Brevo, puis renverra des réponses JSON.

Le rôle de Slim sera donc de gérer des routes comme :

```http
GET /api/health
GET /api/contacts
GET /api/contacts/12
GET /api/stats/dashboard
GET /api/segments
POST /api/sync/weezevent
POST /api/sync/brevo
```

Chaque route correspond à une action précise. Par exemple, `GET /api/contacts` renverra la liste des contacts, tandis que `POST /api/sync/weezevent` déclenchera une récupération de données depuis Weezevent.

## 2. Ce que vous devez comprendre avant de commencer

Avant d’écrire du code, il faut distinguer trois parties.

Le **frontend** correspond à ce que voit l’utilisateur : les pages HTML, les boutons, les tableaux, les formulaires, les cartes statistiques. Dans ce projet, il sera fait avec HTML, Tailwind CSS et JavaScript vanilla.

Le **backend** correspond à la logique serveur : réception des requêtes, vérification des données, connexion à la base, synchronisation avec Weezevent ou Brevo, production des réponses JSON. Dans ce projet, il sera fait avec PHP et Slim.

La **base de données** stocke les informations persistantes : contacts, événements, achats, segments, utilisateurs, synchronisations, logs. Dans ce projet, elle sera faite avec PostgreSQL.

Le schéma général est donc :

```text
Navigateur
   |
   | fetch()
   v
API Slim en PHP
   |
   | PDO
   v
PostgreSQL
```

Et pour les API externes :

```text
API Slim en PHP
   |
   | Requête HTTP avec clé API
   v
Weezevent / Brevo
```

## 3. Préparer le projet

Créez un dossier de projet, par exemple :

```bash
nvb-dashboard-api
```

Placez-vous dedans :

```bash
cd nvb-dashboard-api
```

Initialisez Composer :

```bash
composer init
```

Vous pouvez répondre simplement aux questions. Pour un prototype, ce n’est pas grave si tout n’est pas parfait. Ensuite, installez Slim et les dépendances nécessaires :

```bash
composer require slim/slim:"4.*" slim/psr7 vlucas/phpdotenv guzzlehttp/guzzle
```

Ces dépendances ont chacune un rôle :

```text
slim/slim           : le framework Slim
slim/psr7           : les objets Request et Response utilisés par Slim
vlucas/phpdotenv    : chargement des variables du fichier .env
guzzlehttp/guzzle   : client HTTP pour appeler Brevo ou Weezevent
```

Votre dossier doit commencer à ressembler à ceci :

```text
nvb-dashboard-api/
├── vendor/
├── composer.json
├── composer.lock
```

Le dossier `vendor` contient les bibliothèques installées par Composer. Il ne doit jamais être modifié à la main.

## 4. Structure conseillée du projet

Pour éviter de tout mettre dans un seul fichier, utilisez une structure simple :

```text
nvb-dashboard-api/
├── public/
│   └── index.php
├── src/
│   ├── Database.php
│   ├── Helpers.php
│   ├── Repositories/
│   │   └── ContactRepository.php
│   └── Services/
│       ├── WeezeventService.php
│       └── BrevoService.php
├── routes/
│   ├── contacts.php
│   ├── stats.php
│   ├── segments.php
│   └── sync.php
├── .env
├── .env.example
├── composer.json
└── vendor/
```

Le fichier le plus important est `public/index.php`. C’est le point d’entrée de l’API. Toutes les requêtes passeront par lui.

Le dossier `src` contient le code PHP réutilisable : connexion à la base, fonctions utilitaires, services, repositories.

Le dossier `routes` contient les routes de l’API.

## 5. Créer le fichier `.env`

Le fichier `.env` contient les informations sensibles ou variables selon l’environnement : identifiants de base de données, clés API, URL des services externes.

Créez un fichier `.env` :

```env
APP_ENV=dev
APP_DEBUG=true

DB_HOST=localhost
DB_PORT=5432
DB_NAME=nvb_dashboard
DB_USER=nvb_user
DB_PASSWORD=nvb_password

BREVO_API_KEY=mettre_ici_une_cle_de_test
WEEZEVENT_API_KEY=mettre_ici_une_cle_de_test

BREVO_BASE_URL=https://api.brevo.com/v3
WEEZEVENT_BASE_URL=https://api.weezevent.com
```

Créez aussi un fichier `.env.example`, sans vraie clé :

```env
APP_ENV=dev
APP_DEBUG=true

DB_HOST=localhost
DB_PORT=5432
DB_NAME=nvb_dashboard
DB_USER=nvb_user
DB_PASSWORD=change_me

BREVO_API_KEY=change_me
WEEZEVENT_API_KEY=change_me

BREVO_BASE_URL=https://api.brevo.com/v3
WEEZEVENT_BASE_URL=https://api.weezevent.com
```

Le fichier `.env` ne doit pas être envoyé sur Git. Il doit être ajouté au `.gitignore`.

Créez un fichier `.gitignore` :

```gitignore
/vendor
.env
```

## 6. Créer le point d’entrée `public/index.php`

Créez le dossier `public`, puis le fichier `public/index.php`.

```php
<?php

declare(strict_types=1);

use Dotenv\Dotenv;
use Slim\Factory\AppFactory;

require __DIR__ . '/../vendor/autoload.php';

// Chargement du fichier .env
$dotenv = Dotenv::createImmutable(__DIR__ . '/..');
$dotenv->safeLoad();

// Création de l'application Slim
$app = AppFactory::create();

// Middleware permettant de parser automatiquement le JSON envoyé dans les requêtes
$app->addBodyParsingMiddleware();

// Middleware de gestion des erreurs
$errorMiddleware = $app->addErrorMiddleware(
    ($_ENV['APP_DEBUG'] ?? 'false') === 'true',
    true,
    true
);

// Route de test
$app->get('/api/health', function ($request, $response) {
    $data = [
        'status' => 'ok',
        'message' => 'API Nice Volley Ball opérationnelle',
        'timestamp' => date('Y-m-d H:i:s')
    ];

    $response->getBody()->write(json_encode($data, JSON_UNESCAPED_UNICODE));

    return $response->withHeader('Content-Type', 'application/json');
});

// Chargement des routes
require __DIR__ . '/../routes/contacts.php';
require __DIR__ . '/../routes/stats.php';
require __DIR__ . '/../routes/segments.php';
require __DIR__ . '/../routes/sync.php';

// Lancement de l'application
$app->run();
```

Ce fichier crée l’application Slim, charge les variables d’environnement, ajoute quelques middlewares, définit une route de test, puis charge les autres fichiers de routes.

## 7. Lancer le serveur PHP de développement

Depuis la racine du projet, lancez :

```bash
php -S localhost:8080 -t public
```

Ouvrez ensuite dans le navigateur :

```text
http://localhost:8080/api/health
```

Vous devriez obtenir une réponse JSON de ce type :

```json
{
  "status": "ok",
  "message": "API Nice Volley Ball opérationnelle",
  "timestamp": "2026-06-08 10:30:00"
}
```

Si vous voyez ce JSON, Slim fonctionne.

## 8. Comprendre une route Slim

Une route Slim ressemble à ceci :

```php
$app->get('/api/exemple', function ($request, $response) {
    $data = ['message' => 'Bonjour'];

    $response->getBody()->write(json_encode($data));

    return $response->withHeader('Content-Type', 'application/json');
});
```

Cette route signifie :

```text
Quand le serveur reçoit une requête GET sur /api/exemple,
il exécute cette fonction,
prépare un tableau PHP,
le transforme en JSON,
l’écrit dans la réponse,
puis renvoie la réponse au client.
```

Il existe plusieurs méthodes HTTP :

```text
GET     : récupérer des données
POST    : créer ou déclencher une action
PUT     : remplacer une ressource
PATCH   : modifier partiellement une ressource
DELETE  : supprimer une ressource
```

Dans notre projet, vous utiliserez surtout `GET` et `POST`.

## 9. Créer une fonction utilitaire pour renvoyer du JSON

Écrire à chaque fois `json_encode`, `getBody()->write()` et `withHeader()` devient vite répétitif. Créez un fichier `src/Helpers.php`.

```php
<?php

declare(strict_types=1);

use Psr\Http\Message\ResponseInterface as Response;

function jsonResponse(Response $response, array $data, int $status = 200): Response
{
    $payload = json_encode($data, JSON_UNESCAPED_UNICODE | JSON_PRETTY_PRINT);

    if ($payload === false) {
        $payload = json_encode([
            'success' => false,
            'error' => 'Erreur lors de l’encodage JSON'
        ]);
        $status = 500;
    }

    $response->getBody()->write($payload);

    return $response
        ->withHeader('Content-Type', 'application/json')
        ->withStatus($status);
}
```

Ajoutez ensuite ce fichier dans `public/index.php`, juste après l’autoload :

```php
require __DIR__ . '/../src/Helpers.php';
```

Vous pourrez ensuite écrire des routes plus propres :

```php
$app->get('/api/health', function ($request, $response) {
    return jsonResponse($response, [
        'status' => 'ok',
        'message' => 'API opérationnelle'
    ]);
});
```

## 10. Connexion à PostgreSQL avec PDO

Créez un fichier `src/Database.php`.

```php
<?php

declare(strict_types=1);

final class Database
{
    private static ?PDO $connection = null;

    public static function getConnection(): PDO
    {
        if (self::$connection === null) {
            $host = $_ENV['DB_HOST'] ?? 'localhost';
            $port = $_ENV['DB_PORT'] ?? '5432';
            $dbname = $_ENV['DB_NAME'] ?? 'nvb_dashboard';
            $user = $_ENV['DB_USER'] ?? 'postgres';
            $password = $_ENV['DB_PASSWORD'] ?? '';

            $dsn = "pgsql:host={$host};port={$port};dbname={$dbname}";

            self::$connection = new PDO($dsn, $user, $password, [
                PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
                PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC
            ]);
        }

        return self::$connection;
    }
}
```

Ajoutez dans `public/index.php` :

```php
require __DIR__ . '/../src/Database.php';
```

Cette classe permet d’obtenir une connexion à PostgreSQL avec :

```php
$pdo = Database::getConnection();
```

## 11. Exemple de table `contacts`

Créez une table simple pour démarrer :

```sql
CREATE TABLE contacts (
    id SERIAL PRIMARY KEY,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    phone VARCHAR(30),
    source VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

Ajoutez quelques données de test :

```sql
INSERT INTO contacts (first_name, last_name, email, phone, source)
VALUES
('Alice', 'Martin', 'alice.martin@example.com', '0600000001', 'weezevent'),
('Karim', 'Benali', 'karim.benali@example.com', '0600000002', 'weezevent'),
('Sophie', 'Durand', 'sophie.durand@example.com', '0600000003', 'brevo');
```

## 12. Créer un repository pour les contacts

Un repository est une classe qui regroupe les requêtes SQL liées à une entité. Ici, nous allons créer un repository pour les contacts.

Créez `src/Repositories/ContactRepository.php`.

```php
<?php

declare(strict_types=1);

final class ContactRepository
{
    public function __construct(private PDO $pdo)
    {
    }

    public function findAll(): array
    {
        $sql = "
            SELECT id, first_name, last_name, email, phone, source, created_at
            FROM contacts
            ORDER BY created_at DESC
        ";

        $stmt = $this->pdo->query($sql);

        return $stmt->fetchAll();
    }

    public function findById(int $id): ?array
    {
        $sql = "
            SELECT id, first_name, last_name, email, phone, source, created_at, updated_at
            FROM contacts
            WHERE id = :id
        ";

        $stmt = $this->pdo->prepare($sql);
        $stmt->execute(['id' => $id]);

        $contact = $stmt->fetch();

        return $contact ?: null;
    }

    public function create(array $data): array
    {
        $sql = "
            INSERT INTO contacts (first_name, last_name, email, phone, source)
            VALUES (:first_name, :last_name, :email, :phone, :source)
            RETURNING id, first_name, last_name, email, phone, source, created_at
        ";

        $stmt = $this->pdo->prepare($sql);

        $stmt->execute([
            'first_name' => $data['first_name'],
            'last_name' => $data['last_name'],
            'email' => $data['email'],
            'phone' => $data['phone'] ?? null,
            'source' => $data['source'] ?? 'manual'
        ]);

        return $stmt->fetch();
    }

    public function findByEmail(string $email): ?array
    {
        $sql = "
            SELECT id, first_name, last_name, email, phone, source, created_at
            FROM contacts
            WHERE email = :email
        ";

        $stmt = $this->pdo->prepare($sql);
        $stmt->execute(['email' => $email]);

        $contact = $stmt->fetch();

        return $contact ?: null;
    }
}
```

Ajoutez ce fichier dans `public/index.php` :

```php
require __DIR__ . '/../src/Repositories/ContactRepository.php';
```

## 13. Créer les routes contacts

Créez le fichier `routes/contacts.php`.

```php
<?php

declare(strict_types=1);

use Psr\Http\Message\ResponseInterface as Response;
use Psr\Http\Message\ServerRequestInterface as Request;

$app->get('/api/contacts', function (Request $request, Response $response) {
    try {
        $pdo = Database::getConnection();
        $repository = new ContactRepository($pdo);

        $contacts = $repository->findAll();

        return jsonResponse($response, [
            'success' => true,
            'data' => $contacts
        ]);
    } catch (Throwable $e) {
        return jsonResponse($response, [
            'success' => false,
            'error' => 'Impossible de récupérer les contacts',
            'details' => $e->getMessage()
        ], 500);
    }
});

$app->get('/api/contacts/{id}', function (Request $request, Response $response, array $args) {
    try {
        $id = (int) $args['id'];

        $pdo = Database::getConnection();
        $repository = new ContactRepository($pdo);

        $contact = $repository->findById($id);

        if ($contact === null) {
            return jsonResponse($response, [
                'success' => false,
                'error' => 'Contact introuvable'
            ], 404);
        }

        return jsonResponse($response, [
            'success' => true,
            'data' => $contact
        ]);
    } catch (Throwable $e) {
        return jsonResponse($response, [
            'success' => false,
            'error' => 'Impossible de récupérer le contact',
            'details' => $e->getMessage()
        ], 500);
    }
});

$app->post('/api/contacts', function (Request $request, Response $response) {
    try {
        $data = (array) $request->getParsedBody();

        if (empty($data['first_name']) || empty($data['last_name']) || empty($data['email'])) {
            return jsonResponse($response, [
                'success' => false,
                'error' => 'Les champs first_name, last_name et email sont obligatoires'
            ], 400);
        }

        if (!filter_var($data['email'], FILTER_VALIDATE_EMAIL)) {
            return jsonResponse($response, [
                'success' => false,
                'error' => 'Adresse email invalide'
            ], 400);
        }

        $pdo = Database::getConnection();
        $repository = new ContactRepository($pdo);

        $existingContact = $repository->findByEmail($data['email']);

        if ($existingContact !== null) {
            return jsonResponse($response, [
                'success' => false,
                'error' => 'Un contact existe déjà avec cette adresse email',
                'existing_contact' => $existingContact
            ], 409);
        }

        $contact = $repository->create($data);

        return jsonResponse($response, [
            'success' => true,
            'message' => 'Contact créé',
            'data' => $contact
        ], 201);
    } catch (Throwable $e) {
        return jsonResponse($response, [
            'success' => false,
            'error' => 'Impossible de créer le contact',
            'details' => $e->getMessage()
        ], 500);
    }
});
```

Vous avez maintenant trois routes :

```text
GET  /api/contacts
GET  /api/contacts/{id}
POST /api/contacts
```

## 14. Tester les routes avec le navigateur

La route suivante peut être testée directement dans le navigateur :

```text
http://localhost:8080/api/contacts
```

Vous devriez obtenir :

```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "first_name": "Alice",
      "last_name": "Martin",
      "email": "alice.martin@example.com",
      "phone": "0600000001",
      "source": "weezevent",
      "created_at": "2026-06-08 10:00:00"
    }
  ]
}
```

Pour tester une route `POST`, utilisez Postman, Insomnia, Thunder Client ou `curl`.

Exemple avec `curl` :

```bash
curl -X POST http://localhost:8080/api/contacts \
  -H "Content-Type: application/json" \
  -d '{"first_name":"Lucas","last_name":"Robert","email":"lucas.robert@example.com","phone":"0600000004","source":"manual"}'
```

## 15. Appeler l’API depuis JavaScript vanilla

Dans le frontend, créez un fichier `contacts.js`.

```javascript
async function loadContacts() {
  const tableBody = document.querySelector("#contacts-table-body");

  tableBody.innerHTML = `
    <tr>
      <td colspan="5" class="p-4 text-center text-gray-500">
        Chargement des contacts...
      </td>
    </tr>
  `;

  try {
    const response = await fetch("http://localhost:8080/api/contacts");
    const result = await response.json();

    if (!result.success) {
      throw new Error(result.error || "Erreur inconnue");
    }

    tableBody.innerHTML = "";

    result.data.forEach((contact) => {
      const row = document.createElement("tr");
      row.className = "border-b";

      row.innerHTML = `
        <td class="p-3">${contact.first_name}</td>
        <td class="p-3">${contact.last_name}</td>
        <td class="p-3">${contact.email}</td>
        <td class="p-3">${contact.phone ?? ""}</td>
        <td class="p-3">${contact.source ?? ""}</td>
      `;

      tableBody.appendChild(row);
    });
  } catch (error) {
    tableBody.innerHTML = `
      <tr>
        <td colspan="5" class="p-4 text-center text-red-600">
          Impossible de charger les contacts : ${error.message}
        </td>
      </tr>
    `;
  }
}

document.addEventListener("DOMContentLoaded", loadContacts);
```

Exemple de page HTML :

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8" />
  <title>Contacts — Nice Volley Ball</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 min-h-screen">
  <main class="max-w-6xl mx-auto p-6">
    <h1 class="text-3xl font-bold mb-6">Contacts</h1>

    <div class="bg-white rounded-xl shadow overflow-hidden">
      <table class="w-full text-left">
        <thead class="bg-blue-900 text-white">
          <tr>
            <th class="p-3">Prénom</th>
            <th class="p-3">Nom</th>
            <th class="p-3">Email</th>
            <th class="p-3">Téléphone</th>
            <th class="p-3">Source</th>
          </tr>
        </thead>
        <tbody id="contacts-table-body"></tbody>
      </table>
    </div>
  </main>

  <script src="contacts.js"></script>
</body>
</html>
```

## 16. Attention au CORS

Si votre frontend est servi depuis une adresse différente de l’API, par exemple :

```text
Frontend : http://localhost:3000
API      : http://localhost:8080
```

Le navigateur peut bloquer les requêtes à cause du CORS.

Pour un prototype de développement, ajoutez un middleware CORS simple dans `public/index.php`, avant les routes :

```php
$app->add(function ($request, $handler) {
    $response = $handler->handle($request);

    return $response
        ->withHeader('Access-Control-Allow-Origin', '*')
        ->withHeader('Access-Control-Allow-Headers', 'Content-Type, Authorization')
        ->withHeader('Access-Control-Allow-Methods', 'GET, POST, PUT, PATCH, DELETE, OPTIONS');
});

$app->options('/{routes:.+}', function ($request, $response) {
    return $response;
});
```

Attention : `Access-Control-Allow-Origin: *` est acceptable pour un prototype local, mais pas pour une production réelle avec des données personnelles.

## 17. Créer une route statistiques

Créez le fichier `routes/stats.php`.

```php
<?php

declare(strict_types=1);

use Psr\Http\Message\ResponseInterface as Response;
use Psr\Http\Message\ServerRequestInterface as Request;

$app->get('/api/stats/dashboard', function (Request $request, Response $response) {
    try {
        $pdo = Database::getConnection();

        $totalContacts = (int) $pdo
            ->query("SELECT COUNT(*) FROM contacts")
            ->fetchColumn();

        $weezeventContacts = (int) $pdo
            ->query("SELECT COUNT(*) FROM contacts WHERE source = 'weezevent'")
            ->fetchColumn();

        $brevoContacts = (int) $pdo
            ->query("SELECT COUNT(*) FROM contacts WHERE source = 'brevo'")
            ->fetchColumn();

        return jsonResponse($response, [
            'success' => true,
            'data' => [
                'total_contacts' => $totalContacts,
                'weezevent_contacts' => $weezeventContacts,
                'brevo_contacts' => $brevoContacts
            ]
        ]);
    } catch (Throwable $e) {
        return jsonResponse($response, [
            'success' => false,
            'error' => 'Impossible de récupérer les statistiques',
            'details' => $e->getMessage()
        ], 500);
    }
});
```

Route obtenue :

```text
GET /api/stats/dashboard
```

## 18. Créer une route segments simple

Dans un vrai projet, les segments pourraient être stockés en base et calculés plus finement. Pour le prototype, vous pouvez commencer par des segments simples.

Créez `routes/segments.php`.

```php
<?php

declare(strict_types=1);

use Psr\Http\Message\ResponseInterface as Response;
use Psr\Http\Message\ServerRequestInterface as Request;

$app->get('/api/segments', function (Request $request, Response $response) {
    $segments = [
        [
            'id' => 'nouveaux-visiteurs',
            'label' => 'Nouveaux visiteurs',
            'description' => 'Contacts ayant une seule interaction connue'
        ],
        [
            'id' => 'supporters-reguliers',
            'label' => 'Supporters réguliers',
            'description' => 'Contacts ayant plusieurs interactions connues'
        ],
        [
            'id' => 'abonnes-potentiels',
            'label' => 'Abonnés potentiels',
            'description' => 'Contacts à fort potentiel de fidélisation'
        ]
    ];

    return jsonResponse($response, [
        'success' => true,
        'data' => $segments
    ]);
});

$app->get('/api/segments/{id}/contacts', function (Request $request, Response $response, array $args) {
    try {
        $segmentId = $args['id'];
        $pdo = Database::getConnection();

        if ($segmentId === 'nouveaux-visiteurs') {
            $sql = "
                SELECT id, first_name, last_name, email, phone, source
                FROM contacts
                ORDER BY created_at DESC
                LIMIT 20
            ";
        } elseif ($segmentId === 'supporters-reguliers') {
            $sql = "
                SELECT id, first_name, last_name, email, phone, source
                FROM contacts
                WHERE source = 'weezevent'
                ORDER BY created_at DESC
                LIMIT 20
            ";
        } elseif ($segmentId === 'abonnes-potentiels') {
            $sql = "
                SELECT id, first_name, last_name, email, phone, source
                FROM contacts
                WHERE email IS NOT NULL
                ORDER BY created_at DESC
                LIMIT 20
            ";
        } else {
            return jsonResponse($response, [
                'success' => false,
                'error' => 'Segment inconnu'
            ], 404);
        }

        $contacts = $pdo->query($sql)->fetchAll();

        return jsonResponse($response, [
            'success' => true,
            'segment' => $segmentId,
            'data' => $contacts
        ]);
    } catch (Throwable $e) {
        return jsonResponse($response, [
            'success' => false,
            'error' => 'Impossible de récupérer les contacts du segment',
            'details' => $e->getMessage()
        ], 500);
    }
});
```

Ce code est volontairement simple. Il sert à produire rapidement un prototype. Une version plus aboutie devra s’appuyer sur les vraies tables `achats`, `evenements`, `segments` et `contacts_segments`.

## 19. Préparer un service pour appeler Brevo

Créez `src/Services/BrevoService.php`.

```php
<?php

declare(strict_types=1);

use GuzzleHttp\Client;

final class BrevoService
{
    private Client $client;

    public function __construct()
    {
        $baseUrl = $_ENV['BREVO_BASE_URL'] ?? 'https://api.brevo.com/v3';
        $apiKey = $_ENV['BREVO_API_KEY'] ?? '';

        $this->client = new Client([
            'base_uri' => $baseUrl,
            'headers' => [
                'accept' => 'application/json',
                'content-type' => 'application/json',
                'api-key' => $apiKey
            ],
            'timeout' => 10
        ]);
    }

    public function createOrUpdateContact(array $contact): array
    {
        $payload = [
            'email' => $contact['email'],
            'attributes' => [
                'PRENOM' => $contact['first_name'] ?? '',
                'NOM' => $contact['last_name'] ?? '',
                'TELEPHONE' => $contact['phone'] ?? ''
            ],
            'updateEnabled' => true
        ];

        $response = $this->client->post('/contacts', [
            'json' => $payload
        ]);

        $body = (string) $response->getBody();

        return [
            'status_code' => $response->getStatusCode(),
            'body' => $body !== '' ? json_decode($body, true) : null
        ];
    }
}
```

Ajoutez ce fichier dans `public/index.php` :

```php
require __DIR__ . '/../src/Services/BrevoService.php';
```

Ce service prépare un appel à Brevo. Il faudra vérifier les noms exacts des attributs dans votre compte Brevo de test. Les attributs `PRENOM`, `NOM` et `TELEPHONE` doivent exister ou être adaptés à la configuration du compte.

## 20. Route de test Brevo

Dans `routes/sync.php`, ajoutez :

```php
<?php

declare(strict_types=1);

use Psr\Http\Message\ResponseInterface as Response;
use Psr\Http\Message\ServerRequestInterface as Request;

$app->post('/api/sync/brevo/test-contact', function (Request $request, Response $response) {
    try {
        $data = (array) $request->getParsedBody();

        if (empty($data['email'])) {
            return jsonResponse($response, [
                'success' => false,
                'error' => 'Email obligatoire'
            ], 400);
        }

        $contact = [
            'email' => $data['email'],
            'first_name' => $data['first_name'] ?? 'Test',
            'last_name' => $data['last_name'] ?? 'NVB',
            'phone' => $data['phone'] ?? null
        ];

        $brevo = new BrevoService();
        $result = $brevo->createOrUpdateContact($contact);

        return jsonResponse($response, [
            'success' => true,
            'message' => 'Contact envoyé à Brevo',
            'data' => $result
        ]);
    } catch (Throwable $e) {
        return jsonResponse($response, [
            'success' => false,
            'error' => 'Erreur lors de la synchronisation Brevo',
            'details' => $e->getMessage()
        ], 500);
    }
});
```

Test avec `curl` :

```bash
curl -X POST http://localhost:8080/api/sync/brevo/test-contact \
  -H "Content-Type: application/json" \
  -d '{"email":"test.nvb@example.com","first_name":"Test","last_name":"NVB"}'
```

## 21. Préparer un service Weezevent

L’API Weezevent devra être vérifiée avec la documentation et les identifiants disponibles. Pour commencer proprement, créez un service isolé.

Créez `src/Services/WeezeventService.php`.

```php
<?php

declare(strict_types=1);

use GuzzleHttp\Client;

final class WeezeventService
{
    private Client $client;

    public function __construct()
    {
        $baseUrl = $_ENV['WEEZEVENT_BASE_URL'] ?? 'https://api.weezevent.com';
        $apiKey = $_ENV['WEEZEVENT_API_KEY'] ?? '';

        $this->client = new Client([
            'base_uri' => $baseUrl,
            'headers' => [
                'accept' => 'application/json',
                'Authorization' => 'Bearer ' . $apiKey
            ],
            'timeout' => 10
        ]);
    }

    public function getEvents(): array
    {
        // Attention : endpoint à adapter selon la documentation Weezevent.
        $response = $this->client->get('/events');

        $body = (string) $response->getBody();

        return json_decode($body, true) ?? [];
    }
}
```

Ajoutez dans `public/index.php` :

```php
require __DIR__ . '/../src/Services/WeezeventService.php';
```

Ce code sert de point de départ. L’endpoint `/events` est volontairement à vérifier et adapter selon la documentation Weezevent, le type de compte et les droits API disponibles.

## 22. Route de test Weezevent

Dans `routes/sync.php`, ajoutez :

```php
$app->post('/api/sync/weezevent', function (Request $request, Response $response) {
    try {
        $weezevent = new WeezeventService();

        $events = $weezevent->getEvents();

        return jsonResponse($response, [
            'success' => true,
            'message' => 'Données Weezevent récupérées',
            'data' => $events
        ]);
    } catch (Throwable $e) {
        return jsonResponse($response, [
            'success' => false,
            'error' => 'Erreur lors de la récupération Weezevent',
            'details' => $e->getMessage()
        ], 500);
    }
});
```

L’objectif de cette route est d’abord de vérifier que l’API Weezevent répond. Ensuite, vous pourrez transformer les données reçues pour alimenter la base PostgreSQL.

## 23. Créer une vraie synchronisation Weezevent vers PostgreSQL

Une synchronisation complète doit suivre ces étapes :

```text
1. Appeler l’API Weezevent.
2. Récupérer les événements, billets ou participants.
3. Parcourir les données reçues.
4. Extraire les informations utiles : nom, prénom, email, téléphone, événement, date.
5. Vérifier si le contact existe déjà en base.
6. Créer le contact s’il n’existe pas.
7. Mettre à jour le contact si nécessaire.
8. Créer les événements ou achats associés.
9. Enregistrer un bilan de synchronisation.
10. Retourner un résumé en JSON.
```

Le JSON de réponse pourrait ressembler à ceci :

```json
{
  "success": true,
  "message": "Synchronisation Weezevent terminée",
  "data": {
    "contacts_created": 42,
    "contacts_updated": 13,
    "events_created": 3,
    "errors": 2
  }
}
```

Pour la semaine intensive, il vaut mieux réussir une synchronisation simple et fiable qu’une synchronisation très ambitieuse mais instable.

## 24. Authentification simple

Pour un prototype, vous pouvez créer une table `users`.

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    role VARCHAR(50) NOT NULL DEFAULT 'user',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

Pour générer un mot de passe hashé en PHP :

```php
echo password_hash('admin123', PASSWORD_DEFAULT);
```

Vous insérez ensuite le hash obtenu en base.

Exemple de route `POST /api/login` :

```php
$app->post('/api/login', function (Request $request, Response $response) {
    try {
        $data = (array) $request->getParsedBody();

        if (empty($data['email']) || empty($data['password'])) {
            return jsonResponse($response, [
                'success' => false,
                'error' => 'Email et mot de passe obligatoires'
            ], 400);
        }

        $pdo = Database::getConnection();

        $stmt = $pdo->prepare("
            SELECT id, email, password_hash, role
            FROM users
            WHERE email = :email
        ");

        $stmt->execute([
            'email' => $data['email']
        ]);

        $user = $stmt->fetch();

        if (!$user || !password_verify($data['password'], $user['password_hash'])) {
            return jsonResponse($response, [
                'success' => false,
                'error' => 'Identifiants invalides'
            ], 401);
        }

        return jsonResponse($response, [
            'success' => true,
            'message' => 'Connexion réussie',
            'data' => [
                'id' => $user['id'],
                'email' => $user['email'],
                'role' => $user['role']
            ]
        ]);
    } catch (Throwable $e) {
        return jsonResponse($response, [
            'success' => false,
            'error' => 'Erreur lors de la connexion',
            'details' => $e->getMessage()
        ], 500);
    }
});
```

Cette version est volontairement simplifiée. En production, il faudrait gérer des sessions sécurisées ou des tokens, protéger les routes sensibles et renforcer les contrôles.

## 25. Organisation minimale des fichiers JavaScript côté frontend

Ne mettez pas tout le JavaScript dans un seul fichier. Une organisation simple peut être :

```text
frontend/
├── pages/
│   ├── dashboard.html
│   ├── contacts.html
│   ├── contact-detail.html
│   ├── segments.html
│   └── sync.html
├── js/
│   ├── api.js
│   ├── contacts.js
│   ├── dashboard.js
│   ├── segments.js
│   └── sync.js
└── index.html
```

Le fichier `api.js` peut centraliser les appels :

```javascript
const API_BASE_URL = "http://localhost:8080/api";

async function apiGet(path) {
  const response = await fetch(`${API_BASE_URL}${path}`);
  const data = await response.json();

  if (!response.ok || data.success === false) {
    throw new Error(data.error || "Erreur API");
  }

  return data;
}

async function apiPost(path, payload) {
  const response = await fetch(`${API_BASE_URL}${path}`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(payload)
  });

  const data = await response.json();

  if (!response.ok || data.success === false) {
    throw new Error(data.error || "Erreur API");
  }

  return data;
}
```

Ensuite, dans `contacts.js` :

```javascript
async function loadContacts() {
  const result = await apiGet("/contacts");
  console.log(result.data);
}
```

## 26. Docker Compose minimal pour le développement

Le groupe SISR peut proposer un `docker-compose.yml` comme base.

```yaml
services:
  api:
    image: php:8.3-cli
    working_dir: /app
    volumes:
      - ./:/app
    ports:
      - "8080:8080"
    command: php -S 0.0.0.0:8080 -t public
    depends_on:
      - db

  db:
    image: postgres:16
    environment:
      POSTGRES_DB: nvb_dashboard
      POSTGRES_USER: nvb_user
      POSTGRES_PASSWORD: nvb_password
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  adminer:
    image: adminer
    ports:
      - "8081:8080"
    depends_on:
      - db

volumes:
  postgres_data:
```

Attention : cette image PHP de base ne contient pas forcément l’extension PostgreSQL `pdo_pgsql`. Pour un projet propre, il faudra créer un `Dockerfile`.

Exemple de `Dockerfile` :

```dockerfile
FROM php:8.3-cli

RUN apt-get update \
    && apt-get install -y libpq-dev unzip git \
    && docker-php-ext-install pdo pdo_pgsql

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

WORKDIR /app

CMD ["php", "-S", "0.0.0.0:8080", "-t", "public"]
```

Puis modifiez le service `api` :

```yaml
services:
  api:
    build: .
    working_dir: /app
    volumes:
      - ./:/app
    ports:
      - "8080:8080"
    command: php -S 0.0.0.0:8080 -t public
    depends_on:
      - db
```

Dans `.env`, avec Docker, le host de la base n’est plus `localhost`, mais le nom du service :

```env
DB_HOST=db
DB_PORT=5432
DB_NAME=nvb_dashboard
DB_USER=nvb_user
DB_PASSWORD=nvb_password
```

## 27. Commandes utiles

Installer les dépendances :

```bash
composer install
```

Ajouter une dépendance :

```bash
composer require nom/package
```

Lancer le serveur PHP sans Docker :

```bash
php -S localhost:8080 -t public
```

Lancer avec Docker Compose :

```bash
docker compose up --build
```

Arrêter les conteneurs :

```bash
docker compose down
```

Arrêter et supprimer les volumes :

```bash
docker compose down -v
```

Accéder à Adminer :

```text
http://localhost:8081
```

Paramètres Adminer :

```text
Système   : PostgreSQL
Serveur   : db
Utilisateur : nvb_user
Mot de passe : nvb_password
Base      : nvb_dashboard
```

## 28. Bonnes pratiques à respecter

Ne jamais mettre les clés API directement dans le code. Utilisez le fichier `.env`.

Ne jamais envoyer le fichier `.env` sur Git.

Toujours utiliser des requêtes préparées avec PDO. N’écrivez jamais une requête SQL en concaténant directement des données utilisateur.

Mauvais exemple :

```php
$sql = "SELECT * FROM contacts WHERE email = '$email'";
```

Bon exemple :

```php
$stmt = $pdo->prepare("SELECT * FROM contacts WHERE email = :email");
$stmt->execute(['email' => $email]);
```

Toujours renvoyer du JSON structuré. Une réponse d’API doit être prévisible.

Bon format :

```json
{
  "success": true,
  "data": []
}
```

Ou en cas d’erreur :

```json
{
  "success": false,
  "error": "Message d’erreur clair"
}
```

Ne pas exposer les détails techniques en production. Pendant le développement, les détails d’erreur sont utiles. En production, ils peuvent révéler des informations sensibles.

Ne pas créer trop de routes inutiles. Une API simple et bien documentée vaut mieux qu’une API énorme et confuse.

## 29. Routes minimales attendues pour le projet

À la fin de la semaine, l’API devrait idéalement proposer :

```text
GET  /api/health
POST /api/login

GET  /api/contacts
GET  /api/contacts/{id}
POST /api/contacts

GET  /api/stats/dashboard

GET  /api/segments
GET  /api/segments/{id}/contacts

POST /api/sync/weezevent
POST /api/sync/brevo
POST /api/sync/brevo/test-contact
```

Routes bonus possibles :

```text
GET  /api/sync/history
GET  /api/events
GET  /api/events/{id}
GET  /api/campaigns
POST /api/campaigns
```

## 30. Exemple de documentation de route

Chaque route doit être documentée simplement.

Exemple :

```text
GET /api/contacts

Rôle :
Récupère la liste des contacts enregistrés dans la base.

Paramètres :
Aucun pour la version minimale.

Réponse en cas de succès :
{
  "success": true,
  "data": [
    {
      "id": 1,
      "first_name": "Alice",
      "last_name": "Martin",
      "email": "alice.martin@example.com",
      "phone": "0600000001",
      "source": "weezevent"
    }
  ]
}

Erreurs possibles :
500 si la base de données est inaccessible.
```

## 31. Checklist de réussite pour le backend

Avant la démonstration finale, vérifiez les points suivants :

```text
[ ] Le projet se lance sans erreur.
[ ] La route /api/health répond correctement.
[ ] La connexion PostgreSQL fonctionne.
[ ] Les routes contacts fonctionnent.
[ ] Les erreurs sont renvoyées en JSON.
[ ] Les champs obligatoires sont contrôlés.
[ ] Les emails sont validés.
[ ] Les requêtes SQL utilisent des requêtes préparées.
[ ] Le fichier .env n’est pas versionné.
[ ] Les clés API ne sont pas écrites dans le code.
[ ] Le frontend peut appeler l’API avec fetch().
[ ] La route de statistiques fonctionne.
[ ] La route de segments fonctionne.
[ ] L’appel Brevo de test est documenté.
[ ] L’appel Weezevent est documenté, même s’il est partiel.
```

## 32. Ce qu’il faut éviter

N’essayez pas de recréer Laravel. Slim est volontairement léger.

Ne mettez pas toute la logique dans `public/index.php`. Ce fichier doit rester un point d’entrée.

Ne mélangez pas HTML et API JSON. Le frontend produit le HTML. Slim produit le JSON.

Ne commencez pas par l’authentification complexe. Faites d’abord fonctionner les données, les routes et le dashboard.

Ne perdez pas trop de temps sur des fonctionnalités avancées si les routes de base ne fonctionnent pas.

Ne travaillez pas chacun dans votre coin. Le frontend dépend de l’API, l’API dépend du modèle de données, le déploiement dépend de la structure du projet.

## 33. Objectif pédagogique

À la fin de ce tutoriel, vous devez être capables de :

comprendre le rôle d’un micro-framework ;

créer une API REST simple avec Slim ;

définir des routes GET et POST ;

renvoyer des réponses JSON propres ;

lire des données envoyées par le frontend ;

connecter PHP à PostgreSQL avec PDO ;

utiliser des requêtes préparées ;

appeler une API externe avec Guzzle ;

protéger les clés API avec un fichier `.env` ;

faire communiquer un frontend JavaScript vanilla avec une API PHP ;

documenter vos routes ;

intégrer votre API dans un environnement Docker.

Le but n’est pas seulement de faire fonctionner du code. Le but est de comprendre comment une application web moderne est organisée : un frontend, une API, une base de données, des services externes, des variables d’environnement, des logs, des sauvegardes et une documentation.
