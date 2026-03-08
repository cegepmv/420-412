+++
pre = '<b>9. </b>'
title = "Interceptors"
weight = 10
draft = false
+++

Au départ, la route `whoAmI` dans `UsersController` fonctionnait ainsi :

1. Le contrôleur reçoit la requête
2. Il appelle une méthode du `AuthService`
3. Le service lit le cookie / la session pour récupérer un `userId`
4. Le service retrouve l’utilisateur correspondant

Le problème pédagogique/architectural :
- Le contrôleur “sait trop” comment on retrouve l’utilisateur
- On répète la même logique dans plusieurs routes
- On mélange la logique “transversale” (retrouver l’utilisateur courant) avec la logique d’une route

**Objectif** : éviter de répéter et rendre l’accès à l’utilisateur **courant automatique**.

### Nouvelle approche : `@CurrentUser()` + `CurrentUserInterceptor`
L’idée :

1. Un **interceptor** s’exécute avant le contrôleur
2. Il récupère le `userId` depuis la session / cookie
3. Il appelle `UsersService.findOne(userId)`
4. Il ajoute l’utilisateur dans la requête
5. Un **decorator** lit cette valeur dans la requête
6. La route reçoit directement l’utilisateur

Résultat : la route devient beaucoup plus simple.


##### 1. Créer le décorateur `@CurrentUser()`
Le décorateur sert à **extraire l’utilisateur depuis la requête**.

Il lit simplement une propriété qui sera ajoutée par l’interceptor :

```ts
@Get('/whoami')
whoAmI(@CurrentUser() user: User) {
  return user;
}
```

> Important : le décorateur **ne trouve pas l’utilisateur**. Il se contente de lire `req.currentUser`.

Un décorateur ne peut pas utiliser l’injection de dépendances de NestJS.
Il ne peut donc pas accéder directement aux services comme `UsersService`.

Cela signifie que le décorateur ne peut pas aller chercher l’utilisateur dans la base de données.

Son rôle est uniquement de lire une valeur déjà présente dans la requête.

---

##### 2. Créer `CurrentUserInterceptor`

Le rôle de l’interceptor est de **préparer la requête avant qu’elle arrive au contrôleur**.

Ce qu’il fait :

1. Intercepte la requête
2. Lit le `userId` dans la session
3. Si `userId` existe :
   - appelle `usersService.findOne(userId)`
4. Ajoute l’utilisateur dans la requête :
   
```ts
req.currentUser = user
```

Ensuite la requête continue vers le contrôleur.

Pourquoi l’interceptor peut accéder à `UsersService` ?

Parce que l’interceptor est un **provider NestJS**.
Il bénéficie donc de l’**injection de dépendances**.

---

##### 3. Appliquer l’interceptor

**Option 1 : sur une route**

```ts
@UseInterceptors(CurrentUserInterceptor)
@Get('/whoami')
whoAmI(@CurrentUser() user: User) {
  return user;
}
```

**Option 2 : sur un contrôleur**

```ts
@UseInterceptors(CurrentUserInterceptor)
@Controller('users')
export class UsersController {}
```

**Option 3 : globalement**

Via un provider global (`APP_INTERCEPTOR`) dans un module.

Avantage :
Toutes les routes ont accès à `@CurrentUser()`.

---

### Flow complet de la requête

Lorsqu’un client appelle :

```
GET /users/whoami
```

Le déroulement est le suivant :

1. La requête HTTP arrive avec un cookie / session contenant `userId`
2. `CurrentUserInterceptor` intercepte la requête
3. Il lit `userId`
4. Il appelle `usersService.findOne(userId)`
5. Il ajoute `req.currentUser = user`
6. Le contrôleur s’exécute
7. Le décorateur `@CurrentUser()` lit `req.currentUser`
8. La route retourne l’utilisateur

---

##### En quoi cette approche est meilleure ?

**La séparation des responsabilités**
- Interceptor → prépare la requête
- Decorator → extrait une valeur de la requête
- Controller → définit les routes
- UsersService → logique CRUD

**Lisibilité**

Avant : la route devait appeler un service pour retrouver l’utilisateur.

Après :

```ts
@Get('/whoami')
whoAmI(@CurrentUser() user: User) {
  return user;
}
```
La route est simple et déclarative.


**La réutilisabilité** 

Toutes les routes peuvent accéder à l’utilisateur courant :

```ts
updateProfile(@CurrentUser() user: User) {}
```