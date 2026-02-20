+++
pre = '<b>4. </b>'
title = "Injection de dépendances"
weight = 5
draft = false
+++

# Dependency Injection (NestJS)

La **Dependency Injection (DI)** est un principe fondamental de NestJS.  
Elle permet d’injecter automatiquement des dépendances (ex. services, repositories) dans d’autres classes (ex. contrôleurs) sans les instancier manuellement avec `new`.

L’objectif est de :
- séparer les responsabilités,
- faciliter les tests,
- réduire le couplage entre les classes,
- centraliser la gestion des dépendances dans les modules.

---

### Injection de dépendance dans un module

##### Étape 1 : décorer le service avec `@Injectable()`

Un service doit être injectable pour pouvoir être utilisé ailleurs.

```ts
import { Injectable } from '@nestjs/common';

@Injectable()
export class UsersService {
  findAll() {
    return ['user1', 'user2'];
  }
}
```
`@Injectable()` indique à NestJS que cette classe peut être gérée par le système d’injection de dépendance.

##### Étape 2 : déclarer le service dans les providers du module
```ts
import { Module } from '@nestjs/common';
import { UsersController } from './users.controller';
import { UsersService } from './users.service';

@Module({
  controllers: [UsersController],
  providers: [UsersService],  // important
})
export class UsersModule {}
```
Sans l’ajouter dans `providers`, NestJS ne pourra pas l’injecter.

##### Étape 3 : injecter le service dans le contrôleur
```ts
Copier le code
import { Controller, Get } from '@nestjs/common';
import { UsersService } from './users.service';

@Controller('users')
export class UsersController {
  constructor(private usersService: UsersService) {}

  @Get()
  getUsers() {
    return this.usersService.findAll();
  }
}
```
Ce qui se passe :
NestJS voit que `UsersController` a besoin de `UsersService`
- Il cherche `UsersService` dans les `providers`
- Il crée une instance et l’injecte automatiquement

On n’écrit jamais :
```ts
Copier le code
this.usersService = new UsersService(); ❌
```

### Injection entre modules
Lorsque l’on veut utiliser un service d’un module dans un autre module, il faut l’exporter.

Exemple : AuthModule qui utilise UsersService

**UsersModule**
```ts
@Module({
  controllers: [UsersController],
  providers: [UsersService],
  exports: [UsersService], // permet de l’utiliser ailleurs
})
export class UsersModule {}
````

**AuthModule**
```ts
import { Module } from '@nestjs/common';
import { AuthService } from './auth.service';
import { UsersModule } from '../users/users.module';

@Module({
  imports: [UsersModule], // on importe le module
  providers: [AuthService],
})
export class AuthModule {}
```

**AuthService**
```ts
@Injectable()
export class AuthService {
  constructor(private usersService: UsersService) {}

  login(email: string) {
    return this.usersService.findAll();
  }
}
````

Enfin, pour utiliser un service d’un autre module :

- Le service doit être dans providers
- Il doit être dans exports
- Le module consommateur doit l’ajouter dans imports

Ensuite on peut l’injecter via le constructeur

---

##### En quoi est-ce important ? 

Avec DI :
- NestJS gère le cycle de vie.
- Les dépendances sont centralisées.
- Les services sont facilement remplaçables (mock pour tests).
  
sans DI : 
- Les classes créeraient elles-mêmes leurs dépendances.
- Le code serait fortement couplé.
- Les tests unitaires seraient difficiles.

<!--
À retenir
Un service doit être :
- décoré avec @Injectable()
- déclaré dans providers
- exporté si utilisé dans un autre module
- injecté via le constructeur -->


La ***dependency injection*** dans NestJS repose sur :

✔ les décorateurs (@Injectable())

✔ la configuration des modules (providers, exports, imports)

✔ l’injection via le constructeur

C’est un mécanisme central qui structure l’architecture de l’application.