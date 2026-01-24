# Application Web transactionnelle - projet de session 

À travers ce cours, vous serez amenées à réaliser, en équipe, un projet de développement d’une application Web transactionnelle qui s’échelonnera sur l’ensemble de la session.

Le projet vise à mettre en pratique, de manière intégrée, les notions vues et acquises dans les cours précédents ainsi que celles abordées durant la session, en reproduisant un contexte réaliste de développement logiciel. Il vous permettra d’expérimenter les différentes étapes d’un projet Web, depuis la planification initiale jusqu’à la livraison d’une application fonctionnelle.

Le déroulement de ce projet se fera en deux grandes phases complémentaires.

La première phase est consacrée à la planification, à la conception et à l’apprentissage guidé. Lors de cette période, vous serez appelées à analyser le problème, définir les besoins, identifier les cas d’utilisation, préciser les exigences fonctionnelles et non fonctionnelles, et structurer votre projet à l’aide d’outils et de pratiques issus des méthodologies Agile. Cette phase donne lieu à plusieurs remises intermédiaires et s’accompagne d’un développement encadré, réalisé progressivement avec l’enseignante.

La seconde phase est orientée vers le développement itératif de l’application. Le projet sera alors réalisé en plusieurs itérations de deux semaines, chacune donnant lieu à des livrables clairs et à des démonstrations. Cette phase mènera à la remise finale du projet, comprenant une présentation de l’application ainsi que des entrevues individuelles.

<!-- Certaines fonctionnalités transversales, telles que la gestion des comptes utilisateurs, l’authentification et les mécanismes de base de contrôle d’accès, feront l’objet d’une mise en place commune et guidée. Cela permettra d’assurer une base fonctionnelle uniforme pour l’ensemble des équipes, tout en vous laissant la responsabilité de concevoir et d’implémenter la logique métier propre à votre projet.

Tout au long de la session, vous serez amenées à travailler selon une approche Agile, notamment par la mise en place et le maintien d’un tableau de suivi (Kanban). Même lorsque l’accent sera mis sur l’analyse ou la conception, le projet devra évoluer de manière continue, afin d’aboutir progressivement à un produit fonctionnel, puis à une version finale complète et cohérente. -->

La suite de cet énoncé précise le contexte du projet, les attentes fonctionnelles, l’organisation du travail ainsi que les différentes remises et livrables prévus tout au long de la session.

---

### Contexte général du projet
<!-- Contexte et cadre du projet -->
Un *stack* technologique commun sera présenté et utilisé en classe, dans le cadre de ce projet. L’application développée devra respecter ce *stack*, tant pour le *frontend* que pour le *backend* et la base de données, afin d’assurer une cohérence pédagogique et technique entre les équipes.

Certaines fonctionnalités transversales, notamment la gestion des comptes utilisateurs, l’authentification et les mécanismes de base de contrôle d’accès, feront l’objet d’une mise en place commune et guidée. Cette approche vise à fournir une base fonctionnelle uniforme à l’ensemble des équipes, tout en vous laissant la responsabilité de concevoir, d’implémenter et de faire évoluer la logique métier spécifique à votre projet.

Le développement de l’application se fera selon une approche itérative, inspirée des méthodologies Agile. Tout au long de la session, vous devrez planifier, implémenter et démontrer progressivement les fonctionnalités de votre application, en maintenant un tableau de suivi (Kanban) à jour. 
<!-- Tout au long de la session, vous serez amenées à travailler selon une approche Agile, notamment par la mise en place et le maintien d’un tableau de suivi (Kanban).  -->

##### Exigences générales de l’application
L’application développée devra notamment :
- reposer sur une architecture client–serveur claire, avec une séparation explicite entre le *frontend*, le *backend*, l’API et la base de données ;
- assurer une séparation adéquate entre la logique cliente et la logique d’affaires ;
- exposer une API *backend* simple et cohérente, permettant des transactions pouvant modifier l’état de la base de données ;
- intégrer une logique métier non triviale, implémentée côté serveur ;
- utiliser une base de données transactionnelle, correctement modélisée et implantée sur un serveur local ;
- assurer une gestion des accès et des rôles, incluant des mécanismes de validation administrative lorsque requis ;
- proposer une interface *frontend* fluide, avec une navigation claire et des formulaires fonctionnels ;
- permettre un développement indépendant des composants, notamment en rendant le *backend* testable sans dépendre directement du *frontend* (ex. tests via un client API) ;
- exploiter de manière judicieuse des patrons de conception et de bonnes pratiques architecturales ;
- utiliser des cadriciels modernes, tant côté client que côté serveur.

##### Qualité du code et du développement
Une attention particulière devra être portée à :
- la cohérence et le découplage des modules du service ;
- l’organisation logique des instructions et la lisibilité du code ;
- le choix approprié des algorithmes, des types de données et des structures de données ;
- la gestion complète des erreurs et le bon fonctionnement du service ;
- la présence d’une documentation claire et de tests pour les différentes parties de l’application, afin de faciliter la compréhension, l’utilisation et l’évolution du projet.

--- 

### 🎯 La thématique du projet
Vous devrez définir, en équipe, un projet complet de développement d’une application Web transactionnelle respectant l’ensemble des exigences présentées dans cet énoncé.

Un projet de référence sera proposé par l’enseignante afin d’illustrer le niveau de complexité attendu. Ce projet pourra servir de source d’inspiration pour définir le cadre de votre application, tant sur le plan fonctionnel que sur le plan de la logique métier. Il ne s’agit toutefois pas d’un modèle à reproduire à l’identique.

Le projet de référence porte sur la gestion de l’aide à la réussite au cégep, et comprend notamment les fonctionnalités suivantes :
- la gestion des comptes utilisateurs et des rôles (étudiant·e, enseignant·e, côté administration) ;
- l’inscription à des services à l’aide de formulaires (tutorat, ateliers) ;
- la gestion d’un horaire et de disponibilité des locaux ;
- la validation administrative de certaines actions avant leur publication ;
- la gestion des présences et des disponibilités ;
- un moyen de contact direct avec l’administration via l’application.

Votre équipe n’est pas tenue de reproduire exactement ce projet. Toutefois, l’application développée devra présenter une complexité fonctionnelle et une logique métier équivalentes, incluant des règles de gestion claires et non triviales.

##### Attentes fonctionnelles minimales
L’application développée devra répondre aux exigences fonctionnelles minimales suivantes. Ces exigences constituent la base obligatoire du projet et serviront de référence pour l’évaluation.

| Domaine                       | Exigences                                                                       |
| ----------------------------- | ------------------------------------------------------------------------------- |
| **Gestion des utilisateurs**  | Création de comptes, authentification, rôles et restrictions d’accès            |
| **Fonctionnalités centrales** | Gestion d’un ou plusieurs services, activités ou entités principales            |
| **Formulaires**               | Formulaires permettant l’inscription, la création ou la modification de données |
| **Logique métier**            | Règles de gestion non triviales appliquées côté serveur                         |
| **Validation administrative** | Actions nécessitant une approbation avant d’être visibles ou actives            |
| **Affichage dynamique**       | Présentation d’un état des lieux, d’un horaire ou d’une vue synthèse            |
| **Communication**             | Mécanisme permettant de contacter un responsable ou l’administration            |

Voici le détails par catégorie

**Gestion des comptes et authentification**
- Création de comptes utilisateurs ;
- Authentification (connexion / déconnexion) ;
- Gestion des rôles (ex. utilisateur, administrateur, responsable, etc.) ;
- Restrictions d’accès selon le rôle.

> À noter qu'une partie de cette fonctionnalité sera mise en place collectivement en classe.

**Fonctionnalités principales**
- Gestion d’un élément central propre au projet (services, activités, produits, réservations, etc.) ;
- Création et modification de ces éléments à l’aide de formulaires 
- Affichage de ces éléments sous forme d’état des lieux (horaire, liste dynamique ou équivalent pertinent) ;
- Lien entre ces éléments et les utilisateurs (ou rôles spécifiques).

**Logique métier**
- Implémentation de la logique métier explicite côté *backend* ;
- Gestion de contraintes (ex. disponibilités, conflits, validation, activation/désactivation) ;
- Avec traitement des cas limites et des erreurs.

**Interface utilisateur**
- Une page d’accueil présentant clairement l’application (contexte, services et fonctionnalités offertes);
- Navigation fluide et intuitive ;
- Formulaires fonctionnels et validation des données côté client.

**Communication avec les utilisateurs**
- La présence d’un mécanisme de communication (ex. page "nous contacter") ;
- Avec traitement des demandes côté backend, et message envoyé à l’administration ou à un responsable.


### Organisation du projet et méthodologie
Le projet sera réalisé selon une **approche itérative**, inspirée des méthodologies **Agile**, impliquant des retours réguliers sur le travail effectué. Les livrables produits au fil de la session ne sont pas considérés comme figés : ils pourront être **révisés, ajustés et améliorés** en fonction de l’évolution du projet, des rétroactions reçues et des décisions prises lors des itérations suivantes.

Le travail se fera **en équipe** et devra respecter les éléments suivants :
- Un **tableau de suivi (Kanban)** devra être mis en place dès le début du projet et maintenu à jour tout au long de la session ;
- Les phases d’**analyse**, de **planification** et de **conception** seront guidées par des **livrables** à remettre ;
- Le développement sera structuré en **itérations**, chacune donnant lieu à une **démonstration** ;
- Les fonctionnalités devront être développées de manière **progressive**, **cohérente** et alignée avec le *backlog*.


##### Livrables attendus
Le projet comporte plusieurs remises réparties sur la session.

**1. Mise en place du projet et documentation initiale**
Cette remise vise à faire valider l’idée du projet auprès de l’enseignante et à poser les bases nécessaires à sa structuration, en vue des phases subséquentes du cours, avant le démarrage du développement itératif.

Vous devrez remettre un **rapport** présentant :
- Une description claire de l’application ;
- La problématique et les objectifs (cadre);
  
- Une vue d’ensemble des fonctions de l’application ; 
- La description des utilisateurs et des rôles ;
- Les exigences fonctionnelles et non fonctionnelles ;
- Lees contraintes de l'application ; 
  
- Les cas d’utilisation (*use cases*) ;
  
- La planification à travers la méthodologie de développement ;
- La mise en place du tableau Kanban, incluant un *backlog* initial ;
- L’organisation du dépôt de code, incluant la structure, les accès et les permissions.

**2. Architecture générale et conception de l’API**
Cette remise vise à valider les choix d’architecture et de conception retenus pour votre application, en s’appuyant sur les notions, exemples et exercices réalisés en classe.

À cette étape, vous devrez être en mesure de traduire les besoins fonctionnels identifiés précédemment en une architecture logicielle cohérente, ainsi qu’en une première version structurée de votre API *backend*. Le travail demandé s’inscrit directement dans la continuité des activités guidées réalisées en classe, notamment en ce qui concerne la conception de l’API, la séparation des responsabilités et l’utilisation de patrons architecturaux.

Vous devrez remettre une documentation présentant :
- une description de l’architecture générale de l’application, mettant en évidence les principales couches et leurs interactions ;
- le design de l’API, incluant : les routes principales, les responsabilités associées à chaque route, les opérations supportées (ex. création, lecture, modification, suppression) ;
- des diagrammes pertinents, inspirés de ceux réalisés en classe, tels qu'un diagramme d’architecture, un diagramme de flux ou de séquence, lorsque pertinent ;
- une justification des choix technologiques et architecturaux, en lien avec les besoins du projet et les contraintes identifiées ;
- l’identification et la justification des patrons architecturaux ou de conception utilisés (ex. MVC).

Cette remise doit démontrer que votre équipe est en mesure de concevoir une architecture claire, testable et évolutive, et de documenter adéquatement les décisions prises avant le développement itératif des fonctionnalités.


**3. Modélisation de la base de données**
Cette remise vise à structurer et valider la persistance des données de votre application, en cohérence avec les besoins fonctionnels, la logique métier et l’architecture définis lors des remises précédentes.

À cette étape, vous devrez être en mesure de traduire les exigences de votre application en un modèle de données relationnel cohérent, en vous appuyant sur les notions et exemples vus en classe (modélisation relationnelle, clés primaires et étrangères, contraintes, normalisation, etc.).

Vous devrez remettre une documentation présentant :
- le schéma de la base de données, illustrant l’ensemble des tables et leurs relations ;
- la description détaillée des tables, incluant : les attributs principaux, les types de données, les clés primaires et étrangères, les contraintes et règles d’intégrité, telles que : contraintes de clé, règles d’unicité, dépendances et contraintes métier implémentées au niveau des données ;
- une justification des choix de modélisation, en lien avec : les cas d’utilisation, la logique métier, les contraintes fonctionnelles et techniques du projet.

Cette remise doit démontrer que votre équipe est en mesure de concevoir une base de données transactionnelle robuste, cohérente avec l’architecture globale de l’application, et prête à être utilisée dans les phases de développement et de démonstration à venir.


Les livrables demandés tout au long de la session seront accompagnés de gabarits, d’exemples ou d’indications claires fournies en classe. Il demeure toutefois de votre responsabilité, en tant qu’équipe et à titre individuel, de produire une documentation suffisante, claire, cohérente et rigoureuse, reflétant l’état réel de votre projet.

Une attention particulière sera portée à :
<!-- - la qualité de l’architecture proposée et implémentée ;
- la cohérence de la logique métier ; -->
- la clarté du code, son organisation et sa lisibilité ;
- la progression continue du projet au fil de la session ;
- la capacité à travailler efficacement en équipe ;
- la qualité des démonstrations, tant sur le plan technique que sur le plan de la communication.

L’objectif pédagogique global de ce projet est de favoriser le développement de l’autonomie, du jugement professionnel et de la capacité d’analyse, tout en vous plaçant dans un contexte proche de la réalité du milieu du développement logiciel.

<!-- Ce projet vise ainsi à vous permettre de :
- consolider vos compétences en développement Web ;
- comprendre les enjeux liés à un projet logiciel de moyenne envergure ;
- développer votre autonomie, votre rigueur et votre sens de l’organisation ;
- vous préparer adéquatement aux projets de fin de programme et au contexte professionnel. -->

### Gille de correction – aperçu général
L’évaluation du projet est répartie en deux grandes parties :
la phase de planification et de conception, puis la phase de développement itératif.

**P1 — Avancement et conception du projet**
| Évaluation                                                   | Pondération |
| ------------------------------------------------------------ | ----------- |
| Remise 1 — Documentation initiale et mise en place du projet | 7 %         |
| Remise 2 — Design de l’API et documentation architecturale   | 10 %        |
| Remise 3 — Modélisation de la base de données                | 5 %         |
| Démonstrations de suivi (développement guidé)                | 8 %         |
| **Sous-total**                                               | **30 %**    |

**P2 — Développement itératif des fonctionnalités**
| Évaluation                                 | Pondération |
| ------------------------------------------ | ----------- |
| Démonstration 1                            | 10 %        |
| Démonstration 2                            | 10 %        |
| Démonstration 3 et remise finale du projet | 15 %        |
| Entrevue individuelle                      | 15 %        |
| **Sous-total**                             | **50 %**    |

Les critères détaillés d’évaluation pour chaque remise et chaque démonstration seront précisés au moment opportun et serviront de référence pour la rétroaction et l’évaluation.

##### Information additionnelles 
Le **travail d’équipe et responsabilité individuelle** : bien que ce projet soit réalisé en équipe, il demeure de la responsabilité de chaque personne étudiante de comprendre, d’expliquer et de justifier l’ensemble du travail réalisé dans le cadre du projet.

Il ne sera donc pas acceptable de se dégager de certaines parties du projet en invoquant qu’elles ont été réalisées par un ou une coéquipier·ère.
Le projet est considéré comme une réalisation collective, et chaque membre en est pleinement responsable.

À ce titre, les démonstrations et les remises se feront en équipe complète, sauf indication contraire.

**L'utilisation des outils d’aide et de l’intelligence artificielle** : dans le cadre de ce projet, l’objectif principal est l’apprentissage réel, la compréhension des concepts et le développement de l’autonomie professionnelle.

L’utilisation d’outils externes, incluant les outils d’intelligence artificielle, ne doit en aucun cas :
- remplacer votre réflexion ;
- produire du code ou des livrables que vous ne comprenez pas entièrement ;
- introduire du contenu que vous n’êtes pas en mesure d’expliquer ou de justifier. 

L'entiereté du travail remis et présenté doit provenir de votre compréhension, de vos choix et de votre travail réel.
Vous êtes responsables de l’intégralité du contenu, du code et de la documentation produits dans le cadre du projet.

**Évaluations individuelles**
Enfin, bien que le projet soit évalué en grande partie à l’échelle de l’équipe, cela n’exclut pas des évaluations individuelles, plusieurs mécanismes d’évaluation individuelle, tels que les *quiz* théoriques, les démos de suivi lors de périodes de développement guidées, ou encore les entrevues individuelles finales, permettront d’évaluer les compétences propres à chaque personne étudiante, ainsi que les apprentissages réalisés tout au long de la session.

Ces évaluations visent à assurer que chaque membre de l’équipe a réellement développé les compétences attendues dans le cadre du cours.
