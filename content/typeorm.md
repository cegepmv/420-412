
+++
pre = '<b>6 </b>'
title = "TypeORM"
weight = 8
draft = false
+++

TypeORM fournit un ensemble de **méthodes standardisées** pour gérer les **opérations CRUD**.

La documentation officielle :
- Repository API : https://typeorm.io/docs/working-with-entity-manager/repository-api/
- Working with Repository (NestJs) : https://docs.nestjs.com/techniques/database
  
### Les méthodes principales

##### Lecture
- `find()` → retourne une liste d’entités
- `findOneBy(criteria)` → retourne une entité selon un critère

##### Création
- `create(data)` → crée une instance **sans** sauvegarder en base
- `save(entity)` → sauvegarde en base (insert ou update)

##### Mise à jour
- `update(criteria, partialEntity)` → met à jour sans charger l’entité complète

##### Suppression
- `remove(entity)` → supprime une entité existante
- `delete(criteria)` → supprime directement via un critère

---

### La différence entre `create()` et `save()`

**repository.create()**

- Instancie une entité à partir d’un objet.
- Ne persiste pas en base de données.
- Utile pour préparer les données avant validation ou modification.

**repository.save()**

- Persiste l’entité en base de données.
- Effectue un `INSERT` si l’entité n’existe pas (pas d’id). 
- Effectue un `UPDATE` si elle existe déjà (id existant).
- Retourne l’entité sauvegardée.

### Mise à jour partielle : `update()` vs `findOne()` + `Object.assign()` + `save()`
1. `update(criteria, partialEntity)`

```ts
await repository.update(id, { name: "sara" });
```

Exécute un **UPDATE SQL direct**: 

- Ne charge pas l’entité complète avant modification
- N’exécute pas les *hooks* (`@BeforeUpdate`, etc.)
- Ne retourne pas automatiquement l’entité mise à jour

Utile pour une mise à jour simple et rapide.

2. avec `Object.assign()` + `save()`
```ts  
const user = await repository.findOneBy({ id });

Object.assign(user, updateAttr);

await repository.save(user);
```

Lorsqu’on fait une mise à jour partielle (`PATCH`), le client envoie seulement les champs à modifier :
```ts
{
  "name": "sara"
}
```
On ne veut pas écraser les autres propriétés.

`Object.assign(target, source)` copie uniquement les propriétés présentes dans le DTO, modifie l’objet existant en mémoire
et permet une mise à jour partielle propre. 