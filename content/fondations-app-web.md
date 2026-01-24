+++
pre = '<b>0. </b>'
title = "Fondements"
weight = 1
+++

# développement d’applications Web

Le développement d’une application Web moderne repose sur une combinaison de concepts, d’outils et de choix architecturaux qui permettent de concevoir des systèmes fiables, évolutifs et maintenables. Avant d’entrer dans l’implémentation concrète d’un projet, il est essentiel de comprendre comment une application Web est structurée, comment ses différentes couches interagissent et pourquoi certaines technologies sont privilégiées.

Cette page présente les fondements nécessaires à la compréhension et à la mise en œuvre d’un projet de développement Web moderne. Elle aborde l’architecture générale d’une application transactionnelle et les principes qui la sous-tendent, notamment la séparation claire des responsabilités entre le client, le serveur, l’API et la base de données, ainsi qu’un aperçu des technologies et du *stack* utilisés dans le cadre de ce cours, lesquels serviront de base au projet à réaliser.
<!-- lesquels serviront de base au projet réalisé dans le cadre de ce cours. -->

### Qu’est-ce qu’une application Web transactionnelle ?
Une application Web transactionnelle est une application qui permet à des personnes utilisatrices **d’interagir avec des données persistantes**, généralement stockées dans une base de données, en effectuant des opérations telles que :
- créer, modifier ou supprimer des données ;
- s’authentifier et gérer une session ;
- déclencher des règles métier et des mécanismes de validations ;
- consulter des données mises à jour en temps réel ou quasi réel.

Contrairement à un simple site Web statique, une application Web transactionnelle repose sur une **logique serveur**, une **base de données**, et des **échanges structurés** entre le client et le serveur.

---

### Structure générale d’une application Web

Une application Web moderne repose sur une **séparation claire des responsabilités**, généralement organisée en plusieurs couches distinctes, chacune jouant un rôle précis dans le fonctionnement global du système.

{{% tabs groupId="structure-app-web" %}}

{{% tab name="Frontend (côté client)" %}}
Le *frontend* correspond à la **partie visible et interactive** de l’application.  
Il s’exécute dans le navigateur et permet :

- l’affichage des pages et des composants visuels ;
- la gestion de l'état et des interactions (clics, formulaires, navigation) ;
- la communication avec le *backend* via l’API (au moyen de requêtes HTTP).

C’est la couche avec laquelle la personne utilisatrice interagit directement.
{{% /tab %}}

{{% tab name="Backend (côté serveur)" %}}
Le *backend* contient la **logique métier** de l’application.  
Il est responsable de :

- traiter les requêtes provenant du *frontend* (exposition de l’API) ;
- appliquer les règles de validation et de sécurité ;
- gérer l’authentification et les autorisations ;
- interagir avec la base de données.

Cette couche centralise les règles de fonctionnement de l’application.
{{% /tab %}}

{{% tab name="API (interface de communication)" %}}
L’API agit comme un **contrat** entre le *frontend* et le *backend*.  
Elle définit :

- les routes accessibles ;
- les formats de données échangés (souvent JSON) ;
- les règles d’accès et de validation (sécurité des échanges).

Elle permet de découpler le client du serveur et de structurer les échanges.
{{% /tab %}}

{{% tab name="BD" %}}
La base de données assure la **persistance des données**.  
Elle permet :

- le stockage structuré de l’information ;
- la cohérence et l’intégrité des données ;
- l’exécution de transactions fiables.

Elle garantit que les données sont conservées et accessibles de façon sécurisée.
{{% /tab %}}
{{% /tabs %}}

<!-- Vue d’ensemble des couches
[ Navigateur ]
↓
[ Frontend ]
↓ (HTTP / API)
[ Backend ]
↓
[ Base de données ] -->

Cette séparation permet une application :
- plus **maintenable**,
- plus **évolutive**,
- plus **sécurisée**,
- et plus facile à tester.

### Les technologies possibles par couche

Les technologies utilisées dans une application Web varient selon la couche concernée.  
Les choix suivants illustrent les **langages, *frameworks* et styles les plus couramment utilisés**.

{{% tabs groupId="technos-par-couche" %}}

{{% tab name="Frontend (côté client)" %}}

**Langages** : HTML · CSS · JavaScript / TypeScript

<div style="display:flex; gap:14px; align-items:center; flex-wrap:wrap; margin:10px 0 18px;">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/html5/html5-original.svg" height="44" alt="HTML"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/css3/css3-original.svg" height="44" alt="CSS"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/javascript/javascript-original.svg" height="44" alt="JavaScript"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/typescript/typescript-original.svg" height="44" alt="TypeScript"/>
</div>

**Frameworks / librairies** : React · Angular · Vue.js

<div style="display:flex; gap:14px; align-items:center; flex-wrap:wrap; margin:10px 0;">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/react/react-original.svg" height="44" alt="React"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/angularjs/angularjs-original.svg" height="44" alt="Angular"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/vuejs/vuejs-original.svg" height="44" alt="Vue.js"/>
</div>
{{% /tab %}}

{{% tab name="Backend (côté serveur)" %}}

**Langages** : JavaScript / TypeScript · Java · Python · PHP · C#

<div style="display:flex; gap:14px; align-items:center; flex-wrap:wrap; margin:10px 0 18px;">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/javascript/javascript-original.svg" height="44" alt="JavaScript"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/typescript/typescript-original.svg" height="44" alt="TypeScript"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/java/java-original.svg" height="44" alt="Java"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/python/python-original.svg" height="44" alt="Python"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/php/php-original.svg" height="44" alt="PHP"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/csharp/csharp-original.svg" height="44" alt="C#"/>
</div>

**Frameworks** : NestJS · Express · Spring Boot · Django · Laravel · CodeIgniter · ASP.NET

<div style="display:flex; gap:14px; align-items:center; flex-wrap:wrap; margin:10px 0;">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/nestjs/nestjs-original.svg" height="44" alt="NestJS"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/express/express-original.svg" height="44" alt="Express"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/spring/spring-original.svg" height="44" alt="Spring Boot"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/django/django-plain.svg" height="44" alt="Django"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/laravel/laravel-original.svg" height="44" alt="Laravel"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/codeigniter/codeigniter-plain.svg" height="44" alt="CodeIgniter"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/dot-net/dot-net-original.svg" height="44" alt="ASP.NET"/>
</div>
{{% /tab %}}
<!-- Laravel : le plus populaire aujourd’hui en PHP + très structuré, moderne, riche en fonctionnalités
CodeIgniter : plus léger et plus simple + moins utilisé aujourd’hui, souvent vu comme plus “legacy” -->

{{% tab name="API (communication)" %}}
Le **protocoles** définit comment les messages sont transportés : HTTP / HTTPS · SOAP · gRPC (microservices) · WebSocket (temps réel) · MQTT / AMQP (IoT, event-driven)

Le **style d’API** définit, comment les requêtes sont formulées, comment les réponses sont structurées, et quel(s) format(s) de données sont attendus : REST · GraphQL · WebSocket API · SOAP · RPC / gRPC 

Le **format** définit comment les données sont représentées : JSON · XML · Protobuf
<!-- <p>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/graphql/graphql-plain.svg" height="40"/>
</p> -->
{{% /tab %}}
<!-- Protocole	Rôle	Remarque
HTTP / HTTPS : Transport principal des API Web, le plus courant
WebSocket : Communication bidirectionnelle temps réel, Chat temps réel
gRPC : Communication performante entre services, Microservices
SOAP : Protocole + règles strictes, Plus ancien
MQTT / AMQP	: Messagerie événementielle, IoT et event-driven -->

{{% tab name="Base de données" %}}
<!-- TYPE DE BASE
   ↓
SGBD (système de gestion de base de données)
   ↓
LANGAGE
   ↓
SERVEUR / PLATEFORME -->
**Langages de requêtes** 
- SQL (*Structured Query Language*) : utilisé par les bases **relationnelles**
- MongoDB : requêtes basées sur des documents (en JSON)
- Redis : commandes spécifiques (clé–valeur)
- Cassandra : CQL, langage proche de SQL

**BD relationnelles (SQL)**

Les bases relationnelles organisent les données en tables liées entre elles. 
Chaque SGBD (système de gestion de base de données) possède son dialecte SQL, tout en respectant les principes communs du langage.

- PostgreSQL → SQL + extensions PostgreSQL  
- MySQL → SQL MySQL  
- Oracle Database → SQL Oracle  
- Microsoft SQL Server → T-SQL  
<!-- La base tourne sur un serveur dédié, plusieurs applications peuvent s’y connecter, utilisée en production : PostgreSQL sur un serveur Linux, Oracle sur un serveur d’entreprise, SQL Server sur Windows -->
<div style="display:flex; gap:14px; align-items:center; flex-wrap:wrap; margin:12px 0 18px;">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/postgresql/postgresql-original.svg" height="44" alt="PostgreSQL"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/mysql/mysql-original.svg" height="44" alt="MySQL"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/oracle/oracle-original.svg" height="44" alt="Oracle Database"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/microsoftsqlserver/microsoftsqlserver-plain.svg" height="44" alt="SQL Server"/>
</div>

Certaines bases de données relationnelles ne nécessitent pas de serveur dédié et sont souvent utilisées en développement ou pour des usages locaux :
- SQLite → base relationnelle stockée dans un fichier local (`.db`)
- H2 → base relationnelle légère (environnements Java, souvent en mémoire ou sous forme de fichier)
- DuckDB → base relationnelle orientée analytique, conçue pour l’analyse de données locale et embarquée
<!-- DuckDB est à l’analytique ce que SQLite est au transactionnel, mais optimisé pour les requêtes lourdes (agrégations, scans, joins massifs). -->

<div style="display:flex; gap:14px; align-items:center; flex-wrap:wrap; margin:12px 0;">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/sqlite/sqlite-original.svg" height="44" alt="SQLite"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/h2/h2-original.svg" height="44" alt="H2 Database"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/duckdb/duckdb-original.svg" height="44" alt="DuckDB"/>
</div>

**BD NoSQL (non relationnelles)** 

Les bases NoSQL ne reposent pas sur des tables relationnelles et sont adaptées à des besoins spécifiques :
- MongoDB → base orientée documents (JSON)
- Redis → base clé–valeur
- Cassandra → base orientée colonnes
- Neo4j → base orientée graphes

<div style="display:flex; gap:14px; align-items:center; flex-wrap:wrap; margin:12px 0;">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/mongodb/mongodb-original.svg" height="44" alt="MongoDB"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/redis/redis-original.svg" height="44" alt="Redis"/>
  <img src="https://upload.wikimedia.org/wikipedia/commons/5/5e/Cassandra_logo.svg" height="44" alt="Apache Cassandra"/>
  <img src="https://upload.wikimedia.org/wikipedia/commons/e/e5/Neo4j-logo_color.png" height="44" alt="Neo4j"/>
</div>

**Serveur et plateformes d’hébergement** 

Un SGBD peut être exécuté sur différentes plateformes : Linux · Windows · Docker · Cloud (AWS, Azure, Google Cloud, Oracle Cloud)

Le SGBD reste le même, seule la plateforme de déploiement change.
{{% /tab %}}

{{% /tabs %}}

---

### 🧩 stack technologique utilisée dans le cours

Le projet du cours repose sur une *stack* moderne et cohérente**, représentative des pratiques actuelles en développement d’applications Web transactionnelles. Cette *stack*  permet de couvrir l’ensemble des couches d’une application, du *frontend* jusqu’à la base de données, tout en favorisant une architecture claire et maintenable.

| Couche | Technologies |
|---|---|
| Frontend | React (JavaScript)|
| Backend | NestJS |
| Langage serveur | TypeScript (via Node.js) |
| Base de données (dev) | SQLite |
| Base de données (prod) | PostgreSQL |

##### Quelques précisions techniques
- **Node.js** est l’environnement d’exécution utilisé pour le *backend*. Il permet d’exécuter du JavaScript (ou du TypeScript compilé) côté serveur et constitue la base de l’écosystème utilisé dans le projet.
- **TypeScript** est privilégié afin d’améliorer la robustesse du code, sa lisibilité et la détection d’erreurs avant l’exécution.
- **NestJS** exploite pleinement TypeScript pour proposer une architecture structurée, modulaire et alignée avec les standards professionnelles.
- L’utilisation de **SQLite** en développement permet une mise en place simple et rapide, tandis que la migration vers **PostgreSQL** en fin de projet rapproche l’application d’un contexte de production réel.

**NestJS** est le *framework* *backend* utilisé dans le cadre du cours.
Il se distingue par :
- une architecture claire basée sur des concepts bien définis (controllers, services, modules) ;
- une forte modularité facilitant l’évolution et la maintenance du code ;
- une approche favorisant la testabilité et la séparation des responsabilités.

---

### Objectif de cette fondation
À la fin de cette section, vous devriez être en mesure :
- d'expliquer la structure générale d’une application Web transactionnelle ;
- d'identifier le rôle de chaque couche de l’architecture ;
- d'associer les technologies aux couches auxquelles elles appartiennent ;
- de comprendre les choix technologiques effectués dans le cadre du cours ;
- de situer votre projet dans une architecture Web complète et cohérente.