+++
pre = '<b>3. </b>'
title = "Validation Pipes (NestJS)"
weight = 4
draft = false
+++

Les *Validation Pipes* permettent de valider automatiquement les données entrantes (ex. `body`, `query`, `params`) avant qu’elles n’atteignent la logique métier. L’objectif est d’éviter de traiter des données incomplètes, mal typées ou contenant des propriétés non prévues.

### Étapes essentielles (4 étapes)

##### 1) Activer la validation globale dans `main.ts`

L’activation globale applique la validation à toutes les requêtes qui utilisent des DTO (objets de transfert de données).

```ts
import { ValidationPipe } from '@nestjs/common';
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  app.useGlobalPipes(
    new ValidationPipe({
      whitelist: true,              // supprime les propriétés non définies dans le DTO
      forbidNonWhitelisted: true,   // retourne une erreur si des propriétés inconnues sont envoyées
      transform: true,              // transforme le payload en instance du DTO
    }),
  );

  await app.listen(3000);
}
bootstrap();
```

**Notes pédagogiques** :
- whitelist : protège l’API en ignorant ce qui n’est pas attendu.
- forbidNonWhitelisted : rend cette protection visible (erreur explicite).
- transform : utile pour travailler avec des DTO typés de manière cohérente.

##### 2) Créer un dossier dto et un DTO de validation 
Installer les librairies nécessaires avant de commencer. 
NestJS s’appuie sur deux librairies pour la validation par décorateurs :

```
npm install class-validator class-transformer
```
Ensuite, dans le module concerné (ex. messages), créer :

```
src/messages/dtos/create-message.dto.ts
````
Exemple : validation d’un body contenant *content*

```ts

export class CreateMessageDto {
  content: string;
}
```
##### 3) Appliquer les règles de validation 
Le DTO décrit exactement la forme attendue de la requête (string, longueur minimale, etc.).

```ts
import { IsString, MinLength, MaxLength } from 'class-validator';

export class CreateMessageDto {
  @IsString()
  @MinLength(3)
  @MaxLength(200)
  content: string;
}
```
Les décorateurs (`@IsString`, `@MinLength`, etc.) définissent les règles.

##### 4) Appliquer le DTO au *reqest handler* 
Enfin, on associe le DTO à @Body(), dans le contrôleur.

```ts
import { Body, Controller, Post } from '@nestjs/common';
import { CreateMessageDto } from './dto/create-message.dto';

@Controller('messages')
export class MessagesController {
  @Post()
  createMessage(@Body() body: CreateMessageDto) {
    return body;
  }
}
```

Ce que la validation garantit :
- Si content est absent → erreur 400
- Si content n’est pas une string → erreur 400
- Si content a moins de 3 caractères → erreur 400
- Si le body contient des propriétés non prévues → erreur 400 (si forbidNonWhitelisted: true)

<!-- Résumé

Les DTO décrivent la forme des données attendues.

class-validator fournit les décorateurs de validation.

ValidationPipe exécute automatiquement les validations.

Une configuration globale (useGlobalPipes) assure un comportement cohérent sur toute l’application. -->
