+++
pre = '<b>A. </b>'
title = "JavaScript / TypeScript"
weight = 15
+++

<!-- situer ts par rapport à js, lien vers cours de web pr js révision, surtout tt le necessaire coté front end (react)  -->

TypeScript est un surensemble de JavaScript qui ajoute un système de types statiques au langage. Autrement dit, tout code JavaScript valide est également du code TypeScript, auquel peuvent s’ajouter des annotations de types visant à améliorer la lisibilité, la robustesse et la maintenabilité du code.

**TypeScript = JavaScript + un système de types**

Il est important de souligner que le code TypeScript n’est jamais exécuté directement. Avant toute exécution, le code TypeScript est compilé en JavaScript, et c’est uniquement ce JavaScript généré qui est interprété par le navigateur ou par l’environnement d’exécution Node.js.

Le processus est donc le suivant :
1. Le développeur écrit du code TypeScript (JavaScript enrichi d’annotations de types).
2. Le compilateur TypeScript analyse ce code.
3. Le compilateur génère un équivalent en JavaScript standard.
4. Ce JavaScript est exécuté normalement, sans aucune connaissance de l’utilisation de TypeScript en amont.

**code TypeScript -> compilé en JavaScript -> interprété par le navigateur (env d’exécution Node.js)**

> Un outil interactif permettant d’observer cette transformation est disponible à l’adresse suivante :  
https://www.typescriptlang.org/play

Le système de types de TypeScript intervient **uniquement au moment du développement**. Il permet de détecter un grand nombre d’erreurs potentielles avant l’exécution du programme, mais **n’a aucun impact sur les performances** ou le comportement du code à l’exécution. Une fois la compilation terminée, toutes les informations de type disparaissent.

TypeScript peut ainsi être considéré comme une **couche d’analyse et de validation** qui accompagne le développeur lors de l’écriture du code, tout en produisant, au final, un code JavaScript standard compatible avec l’écosystème existant.

---
<!-- insaller prettier, aller sur settings, chercher format on save et cocher le premier 
theme pour changer des couleurs plus sympa aussi  -->

### Activité guidée : observer l’intérêt de TypeScript
Cette activité introduit un cas d’usage très courant en développement web : effectuer une requête HTTP vers une API, récupérer une réponse JSON, puis exploiter et afficher les données obtenues.  
L’objectif pédagogique est double :
1. Mettre en place un mini-projet Node.js/TypeScript.
2. Montrer concrètement en quoi TypeScript aide à réduire les erreurs lors de la manipulation de données externes.

> API utilisée (API de démonstration) : https://jsonplaceholder.typicode.com.
> Les ressources disponibles sur l’API (exemples) : `/posts` `/comments` `/albums` `/photos` `/todos` `/users`
<!-- montrer l'accès à l'api et l'affichages des todo et d'un todo -->
<!-- https://jsonplaceholder.typicode.com/todos -->

**Objectif de l’exercice**

Le programme devra :
- faire une requête HTTP vers une ressource de l’API 
- afficher le JSON reçu dans la console
- accéder à quelques champs précis (ex. `title`, `completed`)
- illustrer ce qui arrive lorsqu’on se trompe de propriété ou de type
<!-- Insister sur le fait qu’une API externe est une source fréquente d’erreurs : données inattendues, champs manquants, types incohérents.
Annoncer que TypeScript sert précisément à limiter ces erreurs pendant le développement. -->

<!-- --------------------------------------DEMO----------------------------------------------------- -->
<!-- 1. mise en place du projet (dossier + repo Git)
dans un terminal :
```bash
mkdir fetchjson
cd fetchjson
``` -->

<!-- 2. créer un package.json file : pour garder les diff dependencies installer ds notre projet 
```bash
npm init -y
``` -->
<!-- Expliquer que npm init -y crée un projet Node minimal avec un package.json. -->

<!-- 3. installer axios pour effectuer une requête 
npm install axios 
npm install --save-dev @types/node ou npm install -D typescript @types/node
npx tsc --init : compiler  -->

<!-- 4. rédiger notre code 
code .
add index.ts 
... suivre démo -->

### Notions essentielles en TypeScript 
Avant de passer à un projet pratique, cette section propose une révision synthétique des concepts fondamentaux de TypeScript. Ces notions constituent la base nécessaire pour comprendre et structurer des applications plus complexes, notamment avec NestJS.

##### 1) Les types de base en TypeScript
TypeScript introduit un système de types statiques permettant de préciser la nature des données manipulées.

Types primitifs les plus courants :
- `number`
- `string`
- `boolean`

Exemple :
```ts
// premitive types 
let id: number = 1;
let title: string = "Learn TypeScript";
let completed: boolean = false;

// Object types
let date = new Date();
let user = {id:1, name: "sara"};
```
Le typage permet de détecter des incohérences dès l’écriture du code, avant même son exécution.

<!-- type annotation vs type inference  -->
<!-- ```ts
const apple : number = 5; 
const apple : number = "rouge"; // erreur 
``` -->
<!-- initialisation + déclaration sur la mm ligne : type script va utiliser le type inference pr ns 
const color (decla) = rouge (initialisation)
const color : string = rouge

si lignes diff 
const color (decla)
color = rouge 
le type devient any  -->
<!-- si on déclare et initialise sur la mm ligne ca sera le fair direct -->

##### 2) Le typage des fonctions et annotations
Les fonctions peuvent être typées au niveau :
- des paramètres
- de la valeur de retour

Exemple :
```ts
function add(a: number, b: number): number {
  return a + b;
}
```
TypeScript empêche ici l’appel de la fonction avec des arguments incompatibles, renforçant la fiabilité du code.

##### 3) Fichiers de définition de types
Les types peuvent être regroupés dans des définitions réutilisables, notamment à l’aide de :
- type
- interface

Ces définitions servent à décrire la forme des données attendues, particulièrement utiles lors de la consommation d’API externes.

Exemple :
```ts
interface Todo {
  id: number;
  title: string;
  completed: boolean;
}
```

##### 4) Les tableaux en TypeScript
Les tableaux peuvent être typés afin de garantir l’homogénéité des éléments qu’ils contiennent.

Exemples :
```ts
const ids: number[] = [1, 2, 3];

const todos: Todo[] = [
  { id: 1, title: "TS basics", completed: false },
  { id: 2, title: "Interfaces", completed: true }
];

// index.ts
import { Todo } from "./todo";

```
TypeScript empêche l’ajout d’éléments incompatibles dans un tableau typé.

##### 5) Les systèmes de modules
TypeScript repose sur les systèmes de modules JavaScript pour organiser le code en fichiers réutilisables.

Exemple :
```ts
// todo.ts
export interface Todo {
  id: number;
  title: string;
  completed: boolean;
}
```

##### 6) OOP : classes et rappel sur la programmation orientée objet
TypeScript supporte les concepts classiques de la programmation orientée objet :
- classes
- constructeurs
- méthodes
- modificateurs d’accès (public, private, protected)

Exemple :
```ts
class TodoService {
  constructor(private todos: Todo[]) {}

  getCompleted(): Todo[] {
    return this.todos.filter(t => t.completed);
  }
}
```
Ces concepts sont fortement exploités dans les frameworks backend modernes comme NestJS.

<!-- 3)
L'inference 
Any type 
delayed initialisation
Void and never 

4. tuples in ts
5. code reuse with inerface 
6. heritage etc ds interface  -->
---
<!-- ### PROJET PRATIQUE 
Après avoir introduit les fondements de TypeScript, un projet guidé permettra de mettre ces notions en pratique dans un contexte concret de développement front-end. L’objectif est de construire une petite application de cartographie affichant des entités (un utilisateur et une compagnie) sur une carte, à partir de coordonnées géographiques (latitude/longitude).

Ce projet vise à illustrer plusieurs bénéfices immédiats de TypeScript :
- Structurer le code en modules (User.ts, Company.ts, CustomMap.ts, etc.).
- Encadrer la forme des objets via des interfaces (ex. Mappable).
- Réduire les erreurs courantes (propriétés manquantes, types incohérents, mauvais ordre d’arguments).
- Préparer la transition vers NestJS, où ces mêmes réflexes (types, interfaces, classes) sont omniprésents.

**Spécifications minimales de l’application**

L’application devra :
- afficher une carte dans le navigateur ;
- créer un objet User et un objet Company (avec des coordonnées) ;
- afficher un marqueur pour chaque entité ;
- afficher une info-bulle (*popup*) au clic (nom de l’utilisateur / nom de la compagnie).

Le projet sera exécuté en *front-end* à l’aide d’un bundler léger (ex. Parcel).
Une solution open-source sans clé API sera privilégiée pour l’affichage de la carte (ex. Leaflet + OpenStreetMap), afin de faciliter la reproduction de la démo par tous. -->
<!-- --------------------------------------DEMO PROJET----------------------------------------------------- -->
<!-- revision repo git, habituer aux bonnes pratiques -->
