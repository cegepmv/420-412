+++
pre = '<b>1. </b>'
title = "NestJS architecture"
weight = 2
draf = true
+++

NestJS repose sur une architecture modulaire inspirée de concepts issus de *frameworks* comme Angular.  
Elle structure les applications *backend* autour de **modules**, **contrôleurs** et **services**, tout en intégrant un *pipeline* de **traitement des requêtes HTTP**.

Comprendre ce pipeline est essentiel pour savoir où placer la validation, la sécurité, la logique métier et la transformation des réponses.


Image tableau en class. 

<!-- ### Vue globale du cycle d’une requête HTTP
Lorsqu’une requête arrive sur le serveur :

Requête HTTP
↓
Guard
↓
Interceptor (avant)
↓
Pipe
↓
Controller
↓
Service
↓
Repository / Entité (BD)
↓
Interceptor (après)
↓
Réponse HTTP

Chaque couche a une responsabilité spécifique. -->