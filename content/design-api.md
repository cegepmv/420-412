+++
pre = '<b>6 </b>'
title = "API design"
weight = 7
draft = false
+++
---

# Gestion des utilisateurs
Cette section décrit les routes principales liées à l’authentification dans le module **User**, ainsi que l’organisation interne du module (Controller, Service, Repository).

Les routes d’authentification sont regroupées sous le préfixe `/auth`.

| Méthode | Route          | Entrée (Body / Query / Params)                                  | Description                                                                                               |
| ------- | -------------- | --------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| POST    | `/auth/signup` | **Body**: {`email`, `password`, *(optionnel: name, age, etc.)*} | Crée un nouvel utilisateur, valide les données, chiffre le mot de passe et enregistre en base de données. |
| POST    | `/auth/signin` | **Body**: {`email`, `password`}                                 | Authentifie un utilisateur existant, vérifie le mot de passe et retourne une réponse d’authentification.  |

- `signup` → création d’un nouvel utilisateur (écriture en base).
- `signin` → vérification des informations d’identification.
- Le contrôleur reçoit les données (`@Body()`), les services traitent la logique métier, et le repository gère l’accès à la base de données (SQLite pour le moment).
  
### Organisation interne du module `Users`
Le module `Users` regroupe plusieurs composants. On distingue, dans ce projet, **deux services** :

- `UsersService` : opérations CRUD sur les utilisateurs
- `AuthService` : logique d’authentification (signup/signin)
  
### Vue synthèse (structure interne du module User)
  <img
    src="/420-412/images/users-module.png"
    alt="Diagramme du Users Module"
    style="max-width: 85%;"
  />

> Un même module peut contenir **plusieurs controllers** et **plusieurs services**.  .

| Module        | Controller        | Service(s)                                   | Repository / ORM                       | Rôle                                   |
| ------------- | ----------------- | -------------------------------------------- | -------------------------------------- | -------------------------------------- |
| `UsersModule` | `AuthController`  | `AuthService` *(+ depend de `UsersService`)* | *(aucun accès direct)*                 | Authentification : `signup` / `signin` |
| `UsersModule` | `UsersController` | `UsersService`                               | `UsersRepository` (`Repository<User>`) | Gestion des utilisateurs : CRUD        |

Le module `User` est structuré selon une séparation claire des responsabilités :

### 1. UserController
- Définit les routes HTTP (/auth/signup, /auth/signin, etc.).
- Récupère les données via `@Body()`, `@Param()`, `@Query()`.
- Appelle les services : délègue le traitement aux services appropriés.
- Ne contient aucune logique métier complexe.

Son rôle est uniquement de recevoir la requête et d’orchestrer les appels vers les servic

### 2. UserService 
- Contient la logique métier liée aux utilisateurs (CRUD).
  - Crée un utilisateur.
  - Recherche un utilisateur par email.
  - Met à jour ou supprime un utilisateur.
- Communique avec le UsersRepository.

Il ne gère pas l’authentification directement.
Il gère uniquement les opérations liées aux données utilisateur.

ne gère pas l’authentification directement.
Il gère uniquement les opérations liées aux données utilisateur.

### 3.  AuthService
- Contient la logique liée à l’authentification.
  - Vérifie les identifiants (email + mot de passe).
  - Hash le mot de passe lors du signup.
  - Compare les mots de passe lors du signin.
  - Gère éventuellement la génération de tokens (JWT).

### Relation importante : AuthService utilise UsersService.

**Lors d’un signup** :
- `AuthService` reçoit email + password
- *Hash* le mot de passe
- Appelle `UsersService.create()` pour créer l’utilisateur en base

**Lors d’un signin** :
- `AuthService` appelle `UsersService.findByEmail()`
- Compare le mot de passe hashé
- Retourne la réponse d’authentification

`AuthService` ne parle pas directement à la base de données.
Il passe toujours par `UsersService`.

### 3. UserRepository
- Encapsule l’accès à la base de données.
- Utilise TypeORM.
- Exécute les opérations CRUD.
- Ne contient aucune logique métier.

Il ne sait rien de l’authentification.
Il ne fait que manipuler les entités en base.

--- 

### Extension : autres modules (ex. Resources / Forms)

Le même modèle architectural peut être appliqué à d’autres domaines :

- `ResourcesModule`
- `ResourcesController`
- `ResourcesService`
- `ResourcesRepository`

Chaque module représente un **domaine fonctionnel distinct** avec sa propre logique métier.