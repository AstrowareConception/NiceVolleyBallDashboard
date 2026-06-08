# Aide JavaScript vanilla + fetch — Connecter le frontend au backend Slim

## 1. Rôle du JavaScript dans le projet

Dans le projet Nice Volley Ball Dashboard, le frontend est développé sans framework JavaScript. Vous n’utiliserez donc ni React, ni Vue, ni Angular. L’objectif est de renforcer les bases : HTML, Tailwind CSS, JavaScript vanilla, manipulation du DOM, appels API avec `fetch()` et affichage dynamique des données.

Le backend PHP/Slim expose une API REST. Cette API renvoie des données au format JSON. Le rôle du JavaScript est d’appeler cette API, de récupérer les données, puis de mettre à jour les pages HTML.

Le schéma général est le suivant :

```text
Page HTML
   |
   | JavaScript avec fetch()
   v
API Slim
   |
   | PDO
   v
Base PostgreSQL
```

Le JavaScript ne doit pas interroger directement PostgreSQL. Il ne doit pas contenir les clés API Brevo ou Weezevent. Il ne doit pas contenir de requêtes SQL. Il sert uniquement d’intermédiaire entre l’utilisateur et l’API Slim.

## 2. Pages frontend attendues

Le dashboard doit rester simple. Les pages minimales attendues sont les suivantes :

```text
frontend/
├── index.html
├── dashboard.html
├── contacts.html
├── contact-detail.html
├── segments.html
├── sync-weezevent.html
├── sync-brevo.html
├── login.html
└── js/
    ├── config.js
    ├── api.js
    ├── ui.js
    ├── dashboard.js
    ├── contacts.js
    ├── contact-detail.js
    ├── segments.js
    ├── sync-weezevent.js
    ├── sync-brevo.js
    └── auth.js
```

Vous pouvez adapter cette structure, mais l’idée principale doit être respectée : ne mettez pas tout le code JavaScript dans un seul fichier.

Chaque fichier a un rôle :

```text
config.js              → configuration globale, URL de l’API
api.js                 → fonctions génériques pour appeler l’API
ui.js                  → fonctions d’affichage réutilisables
dashboard.js           → logique de la page dashboard
contacts.js            → logique de la liste des contacts
contact-detail.js      → logique de la fiche contact
segments.js            → logique des segments
sync-weezevent.js      → logique de synchronisation Weezevent
sync-brevo.js          → logique de synchronisation Brevo
auth.js                → logique de connexion
```

## 3. Fichier `config.js`

Le fichier `config.js` centralise l’adresse de l’API. Cela évite de répéter `http://localhost:8080/api` partout dans le code.

```javascript
const CONFIG = {
  API_BASE_URL: "http://localhost:8080/api"
};
```

Si l’API change d’adresse, vous ne modifiez qu’un seul fichier.

Exemple :

```javascript
const CONFIG = {
  API_BASE_URL: "http://localhost:8080/api"
};
```

En développement Docker, l’API sera probablement disponible sur `http://localhost:8080/api`. En production, l’adresse pourra devenir différente.

## 4. Fichier `api.js`

Le fichier `api.js` contient les fonctions génériques d’appel API. Toutes les autres pages doivent passer par ces fonctions au lieu d’écrire directement `fetch()` partout.

```javascript
async function apiGet(path) {
  const response = await fetch(`${CONFIG.API_BASE_URL}${path}`);

  let result;
  try {
    result = await response.json();
  } catch (error) {
    throw new Error("La réponse de l’API n’est pas un JSON valide.");
  }

  if (!response.ok || result.success === false) {
    throw new Error(result.error || "Erreur API inconnue.");
  }

  return result;
}

async function apiPost(path, payload = {}) {
  const response = await fetch(`${CONFIG.API_BASE_URL}${path}`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(payload)
  });

  let result;
  try {
    result = await response.json();
  } catch (error) {
    throw new Error("La réponse de l’API n’est pas un JSON valide.");
  }

  if (!response.ok || result.success === false) {
    throw new Error(result.error || "Erreur API inconnue.");
  }

  return result;
}

async function apiPostFormData(path, formData) {
  const response = await fetch(`${CONFIG.API_BASE_URL}${path}`, {
    method: "POST",
    body: formData
  });

  let result;
  try {
    result = await response.json();
  } catch (error) {
    throw new Error("La réponse de l’API n’est pas un JSON valide.");
  }

  if (!response.ok || result.success === false) {
    throw new Error(result.error || "Erreur API inconnue.");
  }

  return result;
}
```

La fonction `apiGet()` sert aux routes de lecture comme :

```text
GET /contacts
GET /contacts/{id}
GET /stats/dashboard
GET /segments
```

La fonction `apiPost()` sert aux routes qui reçoivent du JSON :

```text
POST /login
POST /sync/brevo
```

La fonction `apiPostFormData()` sert aux routes qui reçoivent un fichier :

```text
POST /imports/weezevent
```

## 5. Fichier `ui.js`

Le fichier `ui.js` contient des fonctions d’affichage réutilisables : messages de chargement, messages d’erreur, messages de succès, formatage de dates, formatage de montants.

```javascript
function showLoading(element, message = "Chargement en cours...") {
  element.innerHTML = `
    <div class="p-4 text-center text-gray-500">
      ${message}
    </div>
  `;
}

function showError(element, message = "Une erreur est survenue.") {
  element.innerHTML = `
    <div class="p-4 rounded-lg bg-red-50 text-red-700 border border-red-200">
      ${message}
    </div>
  `;
}

function showSuccess(element, message = "Opération réussie.") {
  element.innerHTML = `
    <div class="p-4 rounded-lg bg-green-50 text-green-700 border border-green-200">
      ${message}
    </div>
  `;
}

function formatDate(value) {
  if (!value) {
    return "—";
  }

  const date = new Date(value);

  if (Number.isNaN(date.getTime())) {
    return value;
  }

  return date.toLocaleDateString("fr-FR", {
    year: "numeric",
    month: "2-digit",
    day: "2-digit"
  });
}

function formatDateTime(value) {
  if (!value) {
    return "—";
  }

  const date = new Date(value);

  if (Number.isNaN(date.getTime())) {
    return value;
  }

  return date.toLocaleString("fr-FR");
}

function formatMoney(value) {
  const amount = Number(value || 0);

  return amount.toLocaleString("fr-FR", {
    style: "currency",
    currency: "EUR"
  });
}

function escapeHtml(value) {
  if (value === null || value === undefined) {
    return "";
  }

  return String(value)
    .replaceAll("&", "&amp;")
    .replaceAll("<", "&lt;")
    .replaceAll(">", "&gt;")
    .replaceAll('"', "&quot;")
    .replaceAll("'", "&#039;");
}
```

La fonction `escapeHtml()` est importante. Elle évite d’injecter directement dans la page du texte potentiellement dangereux.

Mauvais exemple :

```javascript
row.innerHTML = `<td>${contact.email}</td>`;
```

Meilleur exemple :

```javascript
row.innerHTML = `<td>${escapeHtml(contact.email)}</td>`;
```

## 6. Structure HTML commune

Chaque page peut reprendre une structure simple avec Tailwind.

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8" />
  <title>NVB Dashboard</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 min-h-screen">
  <div class="flex min-h-screen">
    <aside class="w-64 bg-blue-950 text-white p-6">
      <h1 class="text-xl font-bold mb-8">NVB Dashboard</h1>

      <nav class="space-y-2">
        <a href="dashboard.html" class="block px-3 py-2 rounded hover:bg-blue-800">Dashboard</a>
        <a href="contacts.html" class="block px-3 py-2 rounded hover:bg-blue-800">Contacts</a>
        <a href="segments.html" class="block px-3 py-2 rounded hover:bg-blue-800">Segments</a>
        <a href="sync-weezevent.html" class="block px-3 py-2 rounded hover:bg-blue-800">Weezevent</a>
        <a href="sync-brevo.html" class="block px-3 py-2 rounded hover:bg-blue-800">Brevo</a>
      </nav>
    </aside>

    <main class="flex-1 p-8">
      <header class="mb-8">
        <h2 class="text-3xl font-bold text-gray-900">Titre de la page</h2>
        <p class="text-gray-600 mt-2">Description courte de la page.</p>
      </header>

      <section id="page-content">
        <!-- Contenu spécifique de la page -->
      </section>
    </main>
  </div>
</body>
</html>
```

Cette structure peut être recopiée puis adaptée pour chaque page.

## 7. Page dashboard

La page `dashboard.html` affiche les indicateurs principaux du projet : nombre de contacts, contacts Weezevent, contacts avec consentement marketing, nombre d’achats, chiffre d’affaires connu, dernière synchronisation.

Exemple de HTML :

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8" />
  <title>Dashboard — NVB</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 min-h-screen">
  <main class="max-w-7xl mx-auto p-8">
    <header class="mb-8">
      <h1 class="text-3xl font-bold text-gray-900">Dashboard Nice Volley Ball</h1>
      <p class="text-gray-600 mt-2">Vue synthétique des contacts, achats et synchronisations.</p>
    </header>

    <div id="dashboard-message" class="mb-4"></div>

    <section id="stats-grid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
      <!-- Cartes injectées en JavaScript -->
    </section>
  </main>

  <script src="js/config.js"></script>
  <script src="js/api.js"></script>
  <script src="js/ui.js"></script>
  <script src="js/dashboard.js"></script>
</body>
</html>
```

Fichier `dashboard.js` :

```javascript
async function loadDashboard() {
  const statsGrid = document.querySelector("#stats-grid");
  const message = document.querySelector("#dashboard-message");

  showLoading(statsGrid, "Chargement des indicateurs...");

  try {
    const result = await apiGet("/stats/dashboard");
    const stats = result.data;

    statsGrid.innerHTML = "";

    const cards = [
      {
        label: "Contacts",
        value: stats.total_contacts ?? 0,
        help: "Nombre total de contacts connus"
      },
      {
        label: "Contacts Weezevent",
        value: stats.weezevent_contacts ?? 0,
        help: "Contacts issus de la billetterie"
      },
      {
        label: "Achats",
        value: stats.total_purchases ?? 0,
        help: "Nombre total d’achats connus"
      },
      {
        label: "CA billetterie",
        value: formatMoney(stats.ticketing_revenue ?? 0),
        help: "Chiffre d’affaires connu"
      }
    ];

    cards.forEach((card) => {
      const article = document.createElement("article");
      article.className = "bg-white rounded-xl shadow p-6";

      article.innerHTML = `
        <p class="text-sm text-gray-500">${escapeHtml(card.label)}</p>
        <p class="text-3xl font-bold text-gray-900 mt-2">${escapeHtml(card.value)}</p>
        <p class="text-sm text-gray-400 mt-2">${escapeHtml(card.help)}</p>
      `;

      statsGrid.appendChild(article);
    });

    message.innerHTML = "";
  } catch (error) {
    statsGrid.innerHTML = "";
    showError(message, `Impossible de charger le dashboard : ${error.message}`);
  }
}

document.addEventListener("DOMContentLoaded", loadDashboard);
```

Route API attendue côté Slim :

```text
GET /api/stats/dashboard
```

Réponse JSON attendue :

```json
{
  "success": true,
  "data": {
    "total_contacts": 128,
    "weezevent_contacts": 102,
    "total_purchases": 240,
    "ticketing_revenue": 3560.50
  }
}
```

## 8. Page liste des contacts

La page `contacts.html` affiche les contacts connus dans un tableau.

HTML :

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8" />
  <title>Contacts — NVB</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 min-h-screen">
  <main class="max-w-7xl mx-auto p-8">
    <header class="mb-8 flex items-center justify-between">
      <div>
        <h1 class="text-3xl font-bold text-gray-900">Contacts</h1>
        <p class="text-gray-600 mt-2">Liste des contacts connus du club.</p>
      </div>

      <a href="sync-weezevent.html" class="px-4 py-2 bg-blue-900 text-white rounded-lg hover:bg-blue-800">
        Synchroniser Weezevent
      </a>
    </header>

    <div id="contacts-message" class="mb-4"></div>

    <section class="bg-white rounded-xl shadow overflow-hidden">
      <table class="w-full text-left">
        <thead class="bg-blue-950 text-white">
          <tr>
            <th class="p-3">Nom</th>
            <th class="p-3">Prénom</th>
            <th class="p-3">Email</th>
            <th class="p-3">Téléphone</th>
            <th class="p-3">Source</th>
            <th class="p-3">Action</th>
          </tr>
        </thead>
        <tbody id="contacts-table-body"></tbody>
      </table>
    </section>
  </main>

  <script src="js/config.js"></script>
  <script src="js/api.js"></script>
  <script src="js/ui.js"></script>
  <script src="js/contacts.js"></script>
</body>
</html>
```

Fichier `contacts.js` :

```javascript
async function loadContacts() {
  const tableBody = document.querySelector("#contacts-table-body");
  const message = document.querySelector("#contacts-message");

  tableBody.innerHTML = `
    <tr>
      <td colspan="6" class="p-4 text-center text-gray-500">
        Chargement des contacts...
      </td>
    </tr>
  `;

  try {
    const result = await apiGet("/contacts");
    const contacts = result.data;

    if (!contacts || contacts.length === 0) {
      tableBody.innerHTML = `
        <tr>
          <td colspan="6" class="p-4 text-center text-gray-500">
            Aucun contact trouvé.
          </td>
        </tr>
      `;
      return;
    }

    tableBody.innerHTML = "";

    contacts.forEach((contact) => {
      const row = document.createElement("tr");
      row.className = "border-b hover:bg-gray-50";

      row.innerHTML = `
        <td class="p-3">${escapeHtml(contact.nom || contact.last_name || "")}</td>
        <td class="p-3">${escapeHtml(contact.prenom || contact.first_name || "")}</td>
        <td class="p-3">${escapeHtml(contact.email || "")}</td>
        <td class="p-3">${escapeHtml(contact.telephone || contact.phone || "")}</td>
        <td class="p-3">
          <span class="px-2 py-1 rounded-full text-xs bg-blue-50 text-blue-700">
            ${escapeHtml(contact.source_origine || contact.source || "inconnue")}
          </span>
        </td>
        <td class="p-3">
          <a href="contact-detail.html?id=${encodeURIComponent(contact.id_contact || contact.id)}"
             class="text-blue-700 hover:underline">
            Voir
          </a>
        </td>
      `;

      tableBody.appendChild(row);
    });

    message.innerHTML = "";
  } catch (error) {
    tableBody.innerHTML = "";
    showError(message, `Impossible de charger les contacts : ${error.message}`);
  }
}

document.addEventListener("DOMContentLoaded", loadContacts);
```

Route API attendue :

```text
GET /api/contacts
```

## 9. Récupérer un paramètre dans l’URL

La page `contact-detail.html` doit savoir quel contact afficher. L’URL sera de ce type :

```text
contact-detail.html?id=12
```

Pour récupérer le paramètre `id` :

```javascript
function getUrlParam(name) {
  const params = new URLSearchParams(window.location.search);
  return params.get(name);
}
```

Utilisation :

```javascript
const contactId = getUrlParam("id");
```

## 10. Page fiche contact

HTML simplifié :

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8" />
  <title>Fiche contact — NVB</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 min-h-screen">
  <main class="max-w-5xl mx-auto p-8">
    <a href="contacts.html" class="text-blue-700 hover:underline">← Retour aux contacts</a>

    <header class="my-8">
      <h1 class="text-3xl font-bold text-gray-900">Fiche contact</h1>
      <p class="text-gray-600 mt-2">Détail d’un contact connu du club.</p>
    </header>

    <div id="contact-message"></div>
    <section id="contact-detail"></section>
  </main>

  <script src="js/config.js"></script>
  <script src="js/api.js"></script>
  <script src="js/ui.js"></script>
  <script src="js/contact-detail.js"></script>
</body>
</html>
```

Fichier `contact-detail.js` :

```javascript
function getUrlParam(name) {
  const params = new URLSearchParams(window.location.search);
  return params.get(name);
}

async function loadContactDetail() {
  const contactId = getUrlParam("id");
  const container = document.querySelector("#contact-detail");
  const message = document.querySelector("#contact-message");

  if (!contactId) {
    showError(message, "Aucun identifiant de contact fourni dans l’URL.");
    return;
  }

  showLoading(container, "Chargement de la fiche contact...");

  try {
    const result = await apiGet(`/contacts/${contactId}`);
    const contact = result.data;

    container.innerHTML = `
      <article class="bg-white rounded-xl shadow p-6">
        <div class="flex items-start justify-between gap-4">
          <div>
            <h2 class="text-2xl font-bold text-gray-900">
              ${escapeHtml(contact.prenom || contact.first_name || "")}
              ${escapeHtml(contact.nom || contact.last_name || "")}
            </h2>
            <p class="text-gray-600 mt-1">${escapeHtml(contact.email || "")}</p>
          </div>

          <span class="px-3 py-1 rounded-full text-sm bg-blue-50 text-blue-700">
            ${escapeHtml(contact.source_origine || contact.source || "inconnue")}
          </span>
        </div>

        <dl class="grid grid-cols-1 md:grid-cols-2 gap-4 mt-8">
          <div>
            <dt class="text-sm text-gray-500">Téléphone</dt>
            <dd class="font-medium">${escapeHtml(contact.telephone || contact.phone || "—")}</dd>
          </div>

          <div>
            <dt class="text-sm text-gray-500">Consentement marketing</dt>
            <dd class="font-medium">${contact.consentement_marketing ? "Oui" : "Non ou inconnu"}</dd>
          </div>

          <div>
            <dt class="text-sm text-gray-500">Date de création</dt>
            <dd class="font-medium">${formatDateTime(contact.date_creation || contact.created_at)}</dd>
          </div>

          <div>
            <dt class="text-sm text-gray-500">Dernière mise à jour</dt>
            <dd class="font-medium">${formatDateTime(contact.date_mise_a_jour || contact.updated_at)}</dd>
          </div>
        </dl>
      </article>
    `;

    message.innerHTML = "";
  } catch (error) {
    container.innerHTML = "";
    showError(message, `Impossible de charger le contact : ${error.message}`);
  }
}

document.addEventListener("DOMContentLoaded", loadContactDetail);
```

Route API attendue :

```text
GET /api/contacts/{id}
```

## 11. Page segments

La page `segments.html` doit afficher les segments disponibles puis, lorsque l’utilisateur clique sur un segment, afficher les contacts concernés.

HTML :

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8" />
  <title>Segments — NVB</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 min-h-screen">
  <main class="max-w-7xl mx-auto p-8">
    <header class="mb-8">
      <h1 class="text-3xl font-bold text-gray-900">Segments</h1>
      <p class="text-gray-600 mt-2">Groupes de contacts exploitables pour les campagnes Brevo.</p>
    </header>

    <div id="segments-message" class="mb-4"></div>

    <section id="segments-list" class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8"></section>

    <section class="bg-white rounded-xl shadow overflow-hidden">
      <div class="p-4 border-b">
        <h2 id="selected-segment-title" class="text-xl font-bold">Contacts du segment</h2>
      </div>

      <table class="w-full text-left">
        <thead class="bg-blue-950 text-white">
          <tr>
            <th class="p-3">Nom</th>
            <th class="p-3">Prénom</th>
            <th class="p-3">Email</th>
            <th class="p-3">Source</th>
          </tr>
        </thead>
        <tbody id="segment-contacts-body">
          <tr>
            <td colspan="4" class="p-4 text-center text-gray-500">
              Sélectionnez un segment.
            </td>
          </tr>
        </tbody>
      </table>
    </section>
  </main>

  <script src="js/config.js"></script>
  <script src="js/api.js"></script>
  <script src="js/ui.js"></script>
  <script src="js/segments.js"></script>
</body>
</html>
```

Fichier `segments.js` :

```javascript
async function loadSegments() {
  const list = document.querySelector("#segments-list");
  const message = document.querySelector("#segments-message");

  showLoading(list, "Chargement des segments...");

  try {
    const result = await apiGet("/segments");
    const segments = result.data;

    list.innerHTML = "";

    segments.forEach((segment) => {
      const card = document.createElement("article");
      card.className = "bg-white rounded-xl shadow p-5 cursor-pointer hover:shadow-md transition";

      card.innerHTML = `
        <h2 class="text-lg font-bold text-gray-900">${escapeHtml(segment.label || segment.nom)}</h2>
        <p class="text-gray-600 text-sm mt-2">${escapeHtml(segment.description || "")}</p>
        <button class="mt-4 px-4 py-2 bg-blue-900 text-white rounded-lg hover:bg-blue-800">
          Voir les contacts
        </button>
      `;

      card.addEventListener("click", () => {
        loadContactsForSegment(segment.id || segment.id_segment, segment.label || segment.nom);
      });

      list.appendChild(card);
    });

    message.innerHTML = "";
  } catch (error) {
    list.innerHTML = "";
    showError(message, `Impossible de charger les segments : ${error.message}`);
  }
}

async function loadContactsForSegment(segmentId, segmentLabel) {
  const title = document.querySelector("#selected-segment-title");
  const tableBody = document.querySelector("#segment-contacts-body");

  title.textContent = `Contacts du segment : ${segmentLabel}`;

  tableBody.innerHTML = `
    <tr>
      <td colspan="4" class="p-4 text-center text-gray-500">
        Chargement des contacts du segment...
      </td>
    </tr>
  `;

  try {
    const result = await apiGet(`/segments/${segmentId}/contacts`);
    const contacts = result.data;

    if (!contacts || contacts.length === 0) {
      tableBody.innerHTML = `
        <tr>
          <td colspan="4" class="p-4 text-center text-gray-500">
            Aucun contact dans ce segment.
          </td>
        </tr>
      `;
      return;
    }

    tableBody.innerHTML = "";

    contacts.forEach((contact) => {
      const row = document.createElement("tr");
      row.className = "border-b hover:bg-gray-50";

      row.innerHTML = `
        <td class="p-3">${escapeHtml(contact.nom || contact.last_name || "")}</td>
        <td class="p-3">${escapeHtml(contact.prenom || contact.first_name || "")}</td>
        <td class="p-3">${escapeHtml(contact.email || "")}</td>
        <td class="p-3">${escapeHtml(contact.source_origine || contact.source || "inconnue")}</td>
      `;

      tableBody.appendChild(row);
    });
  } catch (error) {
    tableBody.innerHTML = `
      <tr>
        <td colspan="4" class="p-4 text-center text-red-600">
          Impossible de charger les contacts : ${escapeHtml(error.message)}
        </td>
      </tr>
    `;
  }
}

document.addEventListener("DOMContentLoaded", loadSegments);
```

Routes API attendues :

```text
GET /api/segments
GET /api/segments/{id}/contacts
```

## 12. Page synchronisation Weezevent

La page `sync-weezevent.html` permet de déclencher une synchronisation avec l’API Weezevent. Dans une version de secours, elle peut aussi permettre d’envoyer un fichier CSV simulé.

HTML :

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8" />
  <title>Synchronisation Weezevent — NVB</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 min-h-screen">
  <main class="max-w-4xl mx-auto p-8">
    <header class="mb-8">
      <h1 class="text-3xl font-bold text-gray-900">Synchronisation Weezevent</h1>
      <p class="text-gray-600 mt-2">
        Récupération des événements, achats et contacts depuis la billetterie.
      </p>
    </header>

    <section class="bg-white rounded-xl shadow p-6 mb-6">
      <h2 class="text-xl font-bold mb-4">Synchronisation API</h2>
      <p class="text-gray-600 mb-4">
        Lance un appel vers l’API backend, qui se charge ensuite d’interroger Weezevent.
      </p>

      <button id="sync-api-button"
              class="px-4 py-2 bg-blue-900 text-white rounded-lg hover:bg-blue-800">
        Lancer la synchronisation Weezevent
      </button>
    </section>

    <section class="bg-white rounded-xl shadow p-6">
      <h2 class="text-xl font-bold mb-4">Import CSV de secours</h2>
      <p class="text-gray-600 mb-4">
        À utiliser si l’API Weezevent n’est pas accessible pendant le prototype.
      </p>

      <form id="csv-import-form" class="space-y-4">
        <input type="file" id="csv-file" accept=".csv"
               class="block w-full border border-gray-300 rounded-lg p-2" />

        <button type="submit"
                class="px-4 py-2 bg-gray-900 text-white rounded-lg hover:bg-gray-800">
          Importer le CSV
        </button>
      </form>
    </section>

    <div id="sync-result" class="mt-6"></div>
  </main>

  <script src="js/config.js"></script>
  <script src="js/api.js"></script>
  <script src="js/ui.js"></script>
  <script src="js/sync-weezevent.js"></script>
</body>
</html>
```

Fichier `sync-weezevent.js` :

```javascript
async function launchWeezeventSync() {
  const resultContainer = document.querySelector("#sync-result");
  const button = document.querySelector("#sync-api-button");

  button.disabled = true;
  button.textContent = "Synchronisation en cours...";
  showLoading(resultContainer, "Connexion à Weezevent et récupération des données...");

  try {
    const result = await apiPost("/sync/weezevent");

    showSuccess(resultContainer, `
      Synchronisation terminée.
      Contacts créés : ${result.data?.contacts_created ?? 0}.
      Contacts mis à jour : ${result.data?.contacts_updated ?? 0}.
      Erreurs : ${result.data?.errors ?? 0}.
    `);
  } catch (error) {
    showError(resultContainer, `Synchronisation impossible : ${error.message}`);
  } finally {
    button.disabled = false;
    button.textContent = "Lancer la synchronisation Weezevent";
  }
}

async function importCsv(event) {
  event.preventDefault();

  const resultContainer = document.querySelector("#sync-result");
  const fileInput = document.querySelector("#csv-file");

  if (!fileInput.files || fileInput.files.length === 0) {
    showError(resultContainer, "Veuillez sélectionner un fichier CSV.");
    return;
  }

  const formData = new FormData();
  formData.append("file", fileInput.files[0]);

  showLoading(resultContainer, "Import du fichier CSV en cours...");

  try {
    const result = await apiPostFormData("/imports/weezevent", formData);

    showSuccess(resultContainer, `
      Import terminé.
      Lignes traitées : ${result.data?.rows_processed ?? 0}.
      Contacts créés : ${result.data?.contacts_created ?? 0}.
      Contacts mis à jour : ${result.data?.contacts_updated ?? 0}.
      Erreurs : ${result.data?.errors ?? 0}.
    `);
  } catch (error) {
    showError(resultContainer, `Import impossible : ${error.message}`);
  }
}

document.addEventListener("DOMContentLoaded", () => {
  document
    .querySelector("#sync-api-button")
    .addEventListener("click", launchWeezeventSync);

  document
    .querySelector("#csv-import-form")
    .addEventListener("submit", importCsv);
});
```

Routes API attendues :

```text
POST /api/sync/weezevent
POST /api/imports/weezevent
```

## 13. Page synchronisation Brevo

La page `sync-brevo.html` permet de synchroniser un segment vers Brevo.

HTML :

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8" />
  <title>Synchronisation Brevo — NVB</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 min-h-screen">
  <main class="max-w-4xl mx-auto p-8">
    <header class="mb-8">
      <h1 class="text-3xl font-bold text-gray-900">Synchronisation Brevo</h1>
      <p class="text-gray-600 mt-2">
        Envoi d’un segment de contacts vers Brevo pour préparer une campagne.
      </p>
    </header>

    <section class="bg-white rounded-xl shadow p-6">
      <form id="brevo-sync-form" class="space-y-4">
        <div>
          <label for="segment-id" class="block text-sm font-medium text-gray-700">
            Segment à synchroniser
          </label>

          <select id="segment-id" class="mt-1 block w-full border border-gray-300 rounded-lg p-2">
            <option value="">Chargement des segments...</option>
          </select>
        </div>

        <button type="submit"
                class="px-4 py-2 bg-blue-900 text-white rounded-lg hover:bg-blue-800">
          Synchroniser vers Brevo
        </button>
      </form>
    </section>

    <div id="brevo-result" class="mt-6"></div>
  </main>

  <script src="js/config.js"></script>
  <script src="js/api.js"></script>
  <script src="js/ui.js"></script>
  <script src="js/sync-brevo.js"></script>
</body>
</html>
```

Fichier `sync-brevo.js` :

```javascript
async function loadSegmentsInSelect() {
  const select = document.querySelector("#segment-id");

  try {
    const result = await apiGet("/segments");
    const segments = result.data;

    select.innerHTML = `<option value="">Sélectionnez un segment</option>`;

    segments.forEach((segment) => {
      const option = document.createElement("option");
      option.value = segment.id || segment.id_segment;
      option.textContent = segment.label || segment.nom;
      select.appendChild(option);
    });
  } catch (error) {
    select.innerHTML = `<option value="">Impossible de charger les segments</option>`;
  }
}

async function syncSegmentToBrevo(event) {
  event.preventDefault();

  const resultContainer = document.querySelector("#brevo-result");
  const segmentId = document.querySelector("#segment-id").value;

  if (!segmentId) {
    showError(resultContainer, "Veuillez sélectionner un segment.");
    return;
  }

  showLoading(resultContainer, "Synchronisation avec Brevo en cours...");

  try {
    const result = await apiPost("/sync/brevo", {
      segment_id: segmentId
    });

    showSuccess(resultContainer, `
      Synchronisation Brevo terminée.
      Contacts envoyés : ${result.data?.contacts_sent ?? 0}.
      Contacts ignorés : ${result.data?.contacts_skipped ?? 0}.
      Erreurs : ${result.data?.errors ?? 0}.
    `);
  } catch (error) {
    showError(resultContainer, `Synchronisation Brevo impossible : ${error.message}`);
  }
}

document.addEventListener("DOMContentLoaded", () => {
  loadSegmentsInSelect();

  document
    .querySelector("#brevo-sync-form")
    .addEventListener("submit", syncSegmentToBrevo);
});
```

Route API attendue :

```text
POST /api/sync/brevo
```

Payload envoyé au backend :

```json
{
  "segment_id": "abonnes-potentiels"
}
```

Réponse attendue :

```json
{
  "success": true,
  "data": {
    "contacts_sent": 24,
    "contacts_skipped": 3,
    "errors": 0
  }
}
```

## 14. Page de connexion simple

L’authentification n’est pas la priorité absolue, mais si elle est réalisée, elle doit rester simple.

HTML `login.html` :

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8" />
  <title>Connexion — NVB</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-blue-950 min-h-screen flex items-center justify-center">
  <main class="bg-white rounded-xl shadow p-8 w-full max-w-md">
    <h1 class="text-2xl font-bold text-gray-900 mb-2">Connexion</h1>
    <p class="text-gray-600 mb-6">Accès interne au dashboard Nice Volley Ball.</p>

    <form id="login-form" class="space-y-4">
      <div>
        <label for="email" class="block text-sm font-medium text-gray-700">Email</label>
        <input type="email" id="email" required
               class="mt-1 block w-full border border-gray-300 rounded-lg p-2" />
      </div>

      <div>
        <label for="password" class="block text-sm font-medium text-gray-700">Mot de passe</label>
        <input type="password" id="password" required
               class="mt-1 block w-full border border-gray-300 rounded-lg p-2" />
      </div>

      <button type="submit"
              class="w-full px-4 py-2 bg-blue-900 text-white rounded-lg hover:bg-blue-800">
        Se connecter
      </button>
    </form>

    <div id="login-message" class="mt-4"></div>
  </main>

  <script src="js/config.js"></script>
  <script src="js/api.js"></script>
  <script src="js/ui.js"></script>
  <script src="js/auth.js"></script>
</body>
</html>
```

Fichier `auth.js` :

```javascript
async function login(event) {
  event.preventDefault();

  const message = document.querySelector("#login-message");
  const email = document.querySelector("#email").value.trim();
  const password = document.querySelector("#password").value;

  if (!email || !password) {
    showError(message, "Veuillez saisir un email et un mot de passe.");
    return;
  }

  showLoading(message, "Connexion en cours...");

  try {
    const result = await apiPost("/login", {
      email,
      password
    });

    localStorage.setItem("nvb_user", JSON.stringify(result.data));

    window.location.href = "dashboard.html";
  } catch (error) {
    showError(message, `Connexion impossible : ${error.message}`);
  }
}

document.addEventListener("DOMContentLoaded", () => {
  document.querySelector("#login-form").addEventListener("submit", login);
});
```

Cette version est volontairement simple. Elle ne remplace pas une authentification sécurisée de production. Pour le prototype, elle permet surtout de comprendre la logique frontend/backend.

## 15. Gestion des états d’interface

Une bonne interface doit gérer plusieurs états :

```text
état vide        → aucune donnée à afficher
état chargement  → requête en cours
état succès      → opération réussie
état erreur      → problème rencontré
état désactivé   → bouton désactivé pendant une action
```

Exemple de bouton désactivé pendant une action :

```javascript
const button = document.querySelector("#sync-button");

button.disabled = true;
button.textContent = "Synchronisation en cours...";

try {
  await apiPost("/sync/weezevent");
} finally {
  button.disabled = false;
  button.textContent = "Lancer la synchronisation";
}
```

Cela évite que l’utilisateur clique cinq fois sur le même bouton.

## 16. Affichage d’un tableau dynamique

Pour afficher un tableau dynamique, la méthode est toujours la même :

```text
1. Sélectionner le tbody.
2. Afficher un état de chargement.
3. Appeler l’API.
4. Vider le tbody.
5. Créer une ligne par élément.
6. Gérer le cas où la liste est vide.
7. Gérer les erreurs.
```

Exemple générique :

```javascript
async function loadTable() {
  const tbody = document.querySelector("#table-body");

  tbody.innerHTML = `
    <tr>
      <td colspan="4" class="p-4 text-center text-gray-500">
        Chargement...
      </td>
    </tr>
  `;

  try {
    const result = await apiGet("/contacts");
    const items = result.data;

    if (items.length === 0) {
      tbody.innerHTML = `
        <tr>
          <td colspan="4" class="p-4 text-center text-gray-500">
            Aucun résultat.
          </td>
        </tr>
      `;
      return;
    }

    tbody.innerHTML = "";

    items.forEach((item) => {
      const row = document.createElement("tr");
      row.innerHTML = `
        <td class="p-3">${escapeHtml(item.nom)}</td>
        <td class="p-3">${escapeHtml(item.prenom)}</td>
      `;
      tbody.appendChild(row);
    });
  } catch (error) {
    tbody.innerHTML = `
      <tr>
        <td colspan="4" class="p-4 text-center text-red-600">
          ${escapeHtml(error.message)}
        </td>
      </tr>
    `;
  }
}
```

## 17. Envoyer un formulaire JSON

Pour envoyer un formulaire classique en JSON :

```html
<form id="contact-form">
  <input type="text" id="prenom" />
  <input type="text" id="nom" />
  <input type="email" id="email" />
  <button type="submit">Créer</button>
</form>
```

JavaScript :

```javascript
async function submitContact(event) {
  event.preventDefault();

  const payload = {
    prenom: document.querySelector("#prenom").value.trim(),
    nom: document.querySelector("#nom").value.trim(),
    email: document.querySelector("#email").value.trim()
  };

  try {
    const result = await apiPost("/contacts", payload);
    console.log("Contact créé", result.data);
  } catch (error) {
    console.error(error.message);
  }
}

document.querySelector("#contact-form").addEventListener("submit", submitContact);
```

## 18. Envoyer un fichier CSV

Pour envoyer un fichier CSV, il faut utiliser `FormData`.

HTML :

```html
<form id="import-form">
  <input type="file" id="csv-file" accept=".csv" />
  <button type="submit">Importer</button>
</form>
```

JavaScript :

```javascript
async function submitCsv(event) {
  event.preventDefault();

  const fileInput = document.querySelector("#csv-file");

  if (!fileInput.files || fileInput.files.length === 0) {
    alert("Veuillez sélectionner un fichier.");
    return;
  }

  const formData = new FormData();
  formData.append("file", fileInput.files[0]);

  try {
    const result = await apiPostFormData("/imports/weezevent", formData);
    console.log("Import terminé", result.data);
  } catch (error) {
    console.error(error.message);
  }
}

document.querySelector("#import-form").addEventListener("submit", submitCsv);
```

Important : quand vous envoyez un `FormData`, il ne faut pas ajouter manuellement l’en-tête `Content-Type`. Le navigateur le fait automatiquement.

## 19. Erreurs fréquentes avec `fetch()`

Erreur fréquente n°1 : oublier `await`.

Mauvais exemple :

```javascript
const response = fetch("/api/contacts");
const data = response.json();
```

Bon exemple :

```javascript
const response = await fetch("/api/contacts");
const data = await response.json();
```

Erreur fréquente n°2 : appeler une mauvaise URL.

```javascript
fetch("/contacts")
```

Si votre API est sur `/api/contacts`, il faut appeler :

```javascript
fetch("http://localhost:8080/api/contacts")
```

D’où l’intérêt du fichier `config.js`.

Erreur fréquente n°3 : oublier que `fetch()` ne considère pas automatiquement une erreur HTTP comme une exception.

Une réponse 404 ou 500 ne déclenche pas forcément un `catch`. C’est pour cela que notre fonction `apiGet()` teste `response.ok`.

Erreur fréquente n°4 : mélanger `innerHTML` et données non sécurisées.

Évitez d’injecter directement des valeurs utilisateur sans protection. Utilisez `escapeHtml()`.

Erreur fréquente n°5 : oublier le CORS.

Si le navigateur bloque l’appel avec une erreur CORS, ce n’est pas forcément votre JavaScript qui est faux. Le backend doit autoriser l’origine du frontend pendant le développement.

## 20. Conventions de réponse API à respecter

Pour que le frontend soit simple, le backend doit renvoyer des réponses cohérentes.

Succès :

```json
{
  "success": true,
  "data": []
}
```

Erreur :

```json
{
  "success": false,
  "error": "Message d’erreur clair"
}
```

Le frontend est beaucoup plus simple si toutes les routes respectent cette convention.

## 21. Contrat entre frontend et backend

Le frontend et le backend doivent se mettre d’accord sur les noms des champs.

Exemple : si le backend renvoie ceci :

```json
{
  "id_contact": 1,
  "nom": "Martin",
  "prenom": "Alice",
  "email": "alice.martin@example.com"
}
```

Le frontend doit utiliser :

```javascript
contact.id_contact
contact.nom
contact.prenom
contact.email
```

Si le backend renvoie plutôt :

```json
{
  "id": 1,
  "last_name": "Martin",
  "first_name": "Alice",
  "email": "alice.martin@example.com"
}
```

Le frontend doit utiliser :

```javascript
contact.id
contact.last_name
contact.first_name
contact.email
```

Il faut donc éviter de changer les noms de champs tous les jours. Le pôle frontend et le pôle backend doivent se coordonner.

## 22. Mini-contrat API recommandé pour le projet

Pour simplifier le travail, vous pouvez adopter ce format côté API.

Contact :

```json
{
  "id_contact": 1,
  "nom": "Martin",
  "prenom": "Alice",
  "email": "alice.martin@example.com",
  "telephone": "0600000001",
  "source_origine": "weezevent",
  "consentement_marketing": true,
  "date_creation": "2026-06-08 10:00:00",
  "date_mise_a_jour": "2026-06-08 10:00:00"
}
```

Segment :

```json
{
  "id_segment": 1,
  "nom": "Supporters réguliers",
  "description": "Contacts ayant au moins deux achats connus",
  "type_segment": "automatique"
}
```

Statistiques dashboard :

```json
{
  "total_contacts": 128,
  "weezevent_contacts": 102,
  "marketing_consent_contacts": 86,
  "total_events": 6,
  "total_purchases": 240,
  "ticketing_revenue": 3560.5,
  "regular_supporters": 42,
  "potential_subscribers": 18
}
```

Bilan de synchronisation :

```json
{
  "sync_id": 12,
  "status": "success",
  "rows_processed": 240,
  "contacts_created": 52,
  "contacts_updated": 31,
  "errors": 0
}
```

## 23. Checklist frontend

Avant de considérer une page comme terminée, vérifiez les points suivants :

```text
[ ] La page se charge sans erreur dans la console.
[ ] Les fichiers JS sont séparés correctement.
[ ] L’API est appelée via apiGet(), apiPost() ou apiPostFormData().
[ ] Les erreurs sont affichées à l’utilisateur.
[ ] Les états de chargement sont visibles.
[ ] Les données sont échappées avec escapeHtml().
[ ] Les boutons sont désactivés pendant les actions longues.
[ ] Les messages de succès sont compréhensibles.
[ ] Les tableaux gèrent le cas “aucune donnée”.
[ ] Les noms de champs correspondent bien aux réponses de l’API.
[ ] La page reste lisible avec Tailwind.
```

## 24. Ce qu’il faut éviter

Ne mettez pas tout le JavaScript dans les pages HTML. Évitez les gros blocs `<script>` directement dans le HTML.

Ne mettez pas tout le projet dans un seul fichier `main.js`. Séparez par page ou par fonctionnalité.

Ne faites pas d’appel direct à Brevo ou Weezevent depuis le frontend. Les clés API doivent rester côté backend.

Ne stockez pas de données sensibles dans `localStorage`, sauf pour un prototype très simplifié. En production, il faudrait une vraie gestion de session ou de token.

Ne laissez pas une page vide si l’API échoue. L’utilisateur doit comprendre ce qui se passe.

Ne supposez pas que l’API répondra toujours correctement. Il faut toujours utiliser `try/catch`.

Ne perdez pas trop de temps sur les effets visuels. La priorité est que le parcours fonctionne : dashboard, contacts, fiche contact, segments, synchronisation.

## 25. Objectif pédagogique

Cette aide doit vous permettre de construire un frontend simple, structuré et connecté à une API REST.

À la fin de ce travail, vous devez être capables de :

* structurer des fichiers JavaScript sans framework ;
* appeler une API avec `fetch()` ;
* gérer les réponses JSON ;
* afficher dynamiquement des données dans une page HTML ;
* gérer un formulaire ;
* envoyer un fichier avec `FormData` ;
* afficher des messages d’erreur et de succès ;
* utiliser Tailwind pour obtenir une interface propre ;
* respecter un contrat API avec le backend ;
* éviter d’exposer des clés API dans le navigateur.

Dans ce projet, le JavaScript n’est pas un simple ajout décoratif. Il permet de transformer des pages HTML statiques en véritable dashboard interactif.
