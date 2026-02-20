+++
pre = '<b>2. </b>'
title = "API HTTP"
weight = 3
draft = false
+++

<!-- API routing  -->


<style>
/* --- Styling léger, compatible Hugo (Goldmark avec unsafe HTML) --- */
.note-wrap { 
  border: 1px solid #e5e7eb; 
  border-radius: 14px; 
  padding: 16px; 
  margin: 16px 0; 
  background: #fff;
}
.note-wrap h2, .note-wrap h3 { margin-top: 0.2rem; }
.kbd { 
  font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
  font-size: 0.95em;
  background: #0b1020; 
  color: #e5e7eb; 
  padding: 2px 8px; 
  border-radius: 8px; 
  border: 1px solid #111827;
}
.callout {
  border-left: 4px solid #111827;
  padding: 10px 12px;
  background: #f9fafb;
  border-radius: 10px;
  margin: 12px 0;
}
.grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 12px;
}
@media (min-width: 900px) {
  .grid { grid-template-columns: 1fr 1fr; }
}
.card {
  border: 1px solid #e5e7eb;
  border-radius: 12px;
  padding: 12px 14px;
  background: #fff;
}
.card h4 { margin: 0 0 8px 0; }
table {
  width: 100%;
  border-collapse: collapse;
}
th, td {
  padding: 10px 12px;
  border-bottom: 1px solid #e5e7eb;
  vertical-align: top;
}
th { background: #f9fafb; text-align: left; }
.muted { color: #6b7280; }
</style>

Cette page résume **les différentes sections d’une requête HTTP** et la récupération des données dans **NestJS** avec `@Param()`, `@Query()` et `@Body()`.

### Structure générale d’une requête HTTP

Une requête HTTP envoyée par un client (navigateur, Postman, front-end, etc.) contient généralement :

- **Méthode** Indique l’action (GET, POST, PUT, DELETE, etc.)
- **URL / Route** Chemin de la ressource (ex. `/users/12`)
- **Params (Route Params)**	Valeurs dynamiques dans l’URL
- **Query string** Données après ? dans l’URL (optionnelle, ex. `?page=2`)
- **Headers** Métadonnées (authentification, *content-type*, etc.)
- **Body** (optionnel) Données envoyées au serveur (JSON, form-data, etc.)

> **Idée-clé** : la route + les paramètres décrivent **où et comment** on agit, le body décrit **quelles données** on envoie.

### Les méthodes HTTP (rappel)

| Méthode    | Usage typique                                  |
| ---------- | ---------------------------------------------- |
| **GET**    | Lire / récupérer des données                   |
| **POST**   | Créer une ressource                            |
| **PUT**    | Remplacer une ressource (mise à jour complète) |
| **PATCH**  | Modifier partiellement une ressource           |
| **DELETE** | Supprimer une ressource                        |

### 1) Paramètres de route — `@Param()` 
Les **params** sont des valeurs **dans l’URL** (souvent un identifiant).

Exemple : <span class="kbd">GET /users/12</span> → `12` est un paramètre.

**NestJS**
```ts
@Get(':id')
findOne(@Param('id') id: string) {
  return `User id: ${id}`;
}
```

**Quand utiliser `param` ?**

✔ Quand l’identifiant fait partie de la structure de la route

✔ Identifier une ressource spécifique

✔ Généralement pour des ID

**Exemples** :
- `GET /users/5`
- `GET /products/10`
- `DELETE /orders/3`

### 2) Paramètres de requête (Query) — `@Query()`

Les **query params** sont après le `?` dans l’URL : filtres, tri, pagination, recherche…

Exemple : <span class="kbd">GET /users?role=admin&page=2</span>

**NestJS**

```ts
@Get()
findAll(
  @Query('role') role?: string,
  @Query('page') page?: string
) {
  return `role: ${role}, page: ${page}`;
}
```

**Quand utiliser `query` ?**

✔ **filtrer** (ex. role=admin)

✔ **paginer** (ex. page=2&limit=10)

✔ **trier** (ex. sort=createdAt)

✔ **rechercher** (ex. search=sara)

**Exemples** :
- `GET /users?role=admin`
- `GET /products?minPrice=10&maxPrice=50`
- `GET /posts?page=2&limit=10`


### 3) Corps de la requête — `@Body()`

Le **body** contient les données envoyées au serveur (souvent JSON).  
On l’utilise principalement avec **POST / PUT / PATCH**.

Exemple : <span class="kbd">POST /users</span> avec un JSON :

```json
{
  "email": "test@mail.com",
  "password": "123456"
}
```

**NestJS (sans DTO)**

```ts
@Post()
create(@Body() body: any) {
  return body;
}
```

**NestJS (avec DTO recommandé)**

```ts
@Post()
create(@Body() dto: CreateUserDto) {
  return dto;
}
```

**Quand utiliser `body` ?**

✔ **créer** une ressource (POST)

✔ **modifier** des données (PUT/PATCH)

✔ envoyer des **données complexes** (objet JSON)


### Comment savoir lequel utiliser ? : param vs query vs body ?
Voici une règle simple :

**Param**

→ Quand tu as besoin d’un identifiant dans l’URL.
- Identifier une ressource précise
- Fait partie de l’URL

GET /users/5

**Query**

→ Quand tu ajoutes des options (souvent facultatives).
- Filtrer / trier / paginer
- Optionnel

GET /users?page=2

**Body**

→ Quand tu crées ou modifies une ressource.
- Envoyer des données
- Créer ou modifier

POST /users

### Exemple combiné (param + query + body)

**Route** : <span class="kbd">PATCH /users/5?notify=true</span>  
**Body**:

```json
{ "email": "new@mail.com" }
```

**NestJS** :

```ts
@Patch(':id')
update(
  @Param('id') id: string,
  @Query('notify') notify: string,
  @Body() body: UpdateUserDto
) {
  return { id, notify, body };
}
```
---

| Type      | Où ?                | Sert à…                   | Exemples             |
| --------- | ------------------- | ------------------------- | -------------------- |
| **Param** | Dans l’URL          | Identifier une ressource  | `/users/5`           |
| **Query** | Après `?`           | Filtrer / trier / paginer | `/users?page=2`      |
| **Body**  | JSON (request body) | Envoyer des données       | `POST /users` + JSON |

##### Quelques bonnes pratiques (REST)
✔ **Nom de ressource** dans l’URL : `/users`, `/messages`, `/reports`

✔ **Action** via la méthode HTTP : GET/POST/PATCH/DELETE

✔ **ID** en param : `/users/:id`

✔ **Filtres** en query : `?page=2&limit=10`

✔ **Données** dans le body : DTO JSON