+++
pre = '<b>10. </b>'
title = "Guards"
weight = 11
draft = false
+++

Actuellement dans notre application, nous avons déjà mis en place :

- un décorateur `@CurrentUser()`
- un `CurrentUserInterceptor`

Cependant, lorsque nous voulons créer un **Guard** pour bloquer certaines routes, nous rencontrons un problème.

Nous voudrions écrire un guard qui vérifie :

- si `req.currentUser` existe
- sinon bloquer l'accès

Helas, cela **ne fonctionne pas**.

##### L'ordre d'exécution dans NestJS est :

```
Middleware
↓
Guard
↓
Interceptor
↓
Controller
```
Donc :

- le **Guard s'exécute avant l'Interceptor**
- `req.currentUser` n'existe pas encore
- le Guard ne peut donc pas vérifier l'utilisateur

### La solution : utiliser un Middleware
Pour résoudre ce problème, nous allons déplacer la logique qui trouve l'utilisateur dans un **middleware**.

Le middleware s'exécute **avant les guards**, donc `req.currentUser` sera disponible pour les guards.

##### Le rôle du middleware
Le middleware :

1. lit `userId` dans la session
2. appelle `UsersService.findOne(userId)`
3. ajoute l'utilisateur dans la requête
4. laisse la requête continuer


##### Exemple de middleware

```ts
import { Injectable, NestMiddleware } from '@nestjs/common';
import { Request, Response, NextFunction } from 'express';
import { UsersService } from '../users.service';

@Injectable()
export class CurrentUserMiddleware implements NestMiddleware {
  constructor(private usersService: UsersService) {}

  async use(req: Request, res: Response, next: NextFunction) {
    const { userId } = req.session || {};

    if (userId) {
      const user = await this.usersService.findOne(userId);
      req.currentUser = user;
    }

    next();
  }
}
```

Maintenant, lorsque la requête arrive :

```
Middleware
↓
Guard
↓
Interceptor
↓
Controller
```

Les guards peuvent lire `req.currentUser`.


---

### Guard : vérifier si l'utilisateur est connecté
Le premier guard que nous allons créer est un **AuthGuard**.

Son rôle est simple :
- vérifier si l'utilisateur est connecté
- sinon bloquer la route

```ts
import { CanActivate, ExecutionContext } from '@nestjs/common';

export class AuthGuard implements CanActivate {
  canActivate(context: ExecutionContext) {
    const request = context.switchToHttp().getRequest();

    return request.session.userId;
  }
}
```
##### Utilisation

```ts
@UseGuards(AuthGuard)
@Get('/profile')
getProfile(@CurrentUser() user: User) {
  return user;
}
```

Si l'utilisateur n'est pas connecté :
- NestJS retourne **401 Unauthorized**.

---

### Guard : vérifier si l'utilisateur est admin

Ensuite, nous pouvons créer un guard pour vérifier si l'utilisateur est **administrateur**.

Pour l'instant, nous supposons que notre entité `User` possède simplement :

```ts
admin: boolean
```

Dans un vrai projet, cela sera amélioré avec : des rôles et des permissions, mais pour l'instant, cette variable suffit pour illustrer le concept.

```ts
import { CanActivate, ExecutionContext } from '@nestjs/common';

export class AdminGuard implements CanActivate {
  canActivate(context: ExecutionContext) {
    const request = context.switchToHttp().getRequest();
    if (!request.currentUser) {
      return false;
    }

    return request.currentUser.admin;
  }
}
```

##### Utilisation

```ts
@UseGuards(AdminGuard)
@Delete('/:id')
deleteUser(@Param('id') id: string) {
  // logique admin
}
```

Si l'utilisateur :
- n'est pas connecté → **401**
- n'est pas admin → **403 Forbidden**

---

### Où enregistrer le middleware ?

Dans le module :

```ts
import { Module, MiddlewareConsumer } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { UsersController } from './users.controller';
import { UsersService } from './users.service';
import { AuthService } from './auth.service';
import { User } from './user.entity';
import { CurrentUserMiddleware } from './middlewares/current-user.middleware';

@Module({
  imports: [TypeOrmModule.forFeature([User])],
  controllers: [UsersController],
  providers: [UsersService, AuthService],
})
export class UsersModule {
  configure(consumer: MiddlewareConsumer) {
    consumer.apply(CurrentUserMiddleware).forRoutes('*');
  }
}
```

Ainsi, toutes les routes auront accès à `req.currentUser`.

---

### En résumé

**Middleware**

- s'exécute **avant les guards**
- peut utiliser l'injection de dépendances
- récupère l'utilisateur
- ajoute `req.currentUser`

**AuthGuard**

- bloque les routes si l'utilisateur n'est pas connecté
- retourne **401 Unauthorized**

**AdminGuard**

- vérifie `user.admin`
- retourne **403 Forbidden** si l'utilisateur n'a pas les droits


Cette architecture permet de séparer les responsabilités :

| Composant  | Rôle                   |
| ---------- | ---------------------- |
| Middleware | Trouver l'utilisateur  |
| Guard      | Autoriser ou bloquer   |
| Decorator  | Extraire l'utilisateur |
| Controller | Définir les routes     |
| Service    | Logique métier         |
