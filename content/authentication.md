+++
pre = '<b>8. </b>'
title = "Authentication"
weight = 9
draft = false
+++

L’authentification permet d’identifier un utilisateur et de s’assurer qu’il est bien celui qu’il prétend être.
Nous utilisons, dans notre application, un mécanisme basé sur les cookies pour maintenir la session entre le client (navigateur) et le serveur.

### Vue globale du flow d’authentification
Le processus se déroule en plusieurs étapes :
##### 1. Inscription / Connexion
Le client envoie une requête HTTP :
```ts
POST /auth/signup
{
  email,
  password
}
```
Le serveur :
- Vérifie si l’email existe déjà
- Chiffre le mot de passe
- Enregistre l’utilisateur en base de données

##### 2. Génération du cookie
Une fois l’utilisateur créé ou authentifié, le serveur :
- Génère un identifiant unique (ex : userId)
- Envoie un cookie au navigateur contenant cet identifiant
```ts
Cookie: userId = 50
```

Le navigateur :
- Stocke automatiquement ce cookie
- L’attache aux requêtes futures vers le même serveur

##### 3. Requêtes authentifiées
Plus tard, lorsqu’un utilisateur fait une nouvelle requête :

```ts
POST /reports
Cookie: userId = 50
{
    data
}
```

Le serveur :
- Lit le cookie
- Vérifie qu’il n’a pas été modifié
- Récupère l’utilisateur correspondant au userId
- Autorise ou refuse la requête

##### Idée clé
Le mot de passe n’est jamais renvoyé après la connexion.
L’identité de l’utilisateur repose uniquement sur le cookie envoyé par le serveur.

Cela permet :
- De maintenir une session
- D’identifier l’utilisateur à chaque requête
- De sécuriser l’accès aux routes protégées

--- 

### Hash du mot de passe
Pourquoi on ne stocke jamais le mot de passe en clair ?

Un mot de passe ne doit jamais être enregistré tel quel dans la base de données.

Si la base est compromise :
- Tous les mots de passe seraient visibles
- Les utilisateurs seraient exposés
- Les mots de passe pourraient être réutilisés sur d’autres sites

Solution : utiliser une fonction de hachage (*hashing* function).

##### Qu’est-ce qu’une fonction de *hash* ?
Une fonction de *hash* :
- Prend une chaîne de caractères en entrée
- Produit une chaîne totalement différente en sortie
- Est déterministe : même entrée → même résultat
- Est non réversible

Exemple :
```ts
password123
→ 5e884898da28047151d0e56f8dc6292773603d0d6aabbdd...
```
Si on passe à nouveau `password123`, on obtiendra exactement le même *hash*.

Alors que si on passe :
`5e884898da280471...`

La fonction va simplement produire un autre *hash*.
Il est impossible de récupérer le mot de passe original à partir du *hash*.

- Ce n’est pas du chiffrement.
- C’est une transformation à sens unique.

Les fonctions de *hash* sont utilisées partout :
- Stockage de mots de passe
- Blockchain
- Signatures numériques
- Vérification d’intégrité

Plutôt que d’implémenter notre propre fonction (ce serait dangereux),
on utilise une librairie sécurisée comme :
`bcrypt`, `argon2`, `scrypt`


##### Comment fonctionne le login sans connaître le mot de passe ?
Lors du *signup*

Le client envoie :
```ts
{ "email": "...", "password": "abc123" }
```

Le serveur :
- *Hash* le mot de passe
- Stocke uniquement le *hash* en base

Exemple en base :
```ts
email: user@test.com
password: $2b$10$X7d9...k29...
```

Lors du *signin*
- L’utilisateur envoie son mot de passe
- Le serveur *hash* le mot de passe reçu
- Compare le *hash* obtenu avec celui en base

Si les deux sont identiques → mot de passe valide

On ne déchiffre rien.
On compare simplement deux hashes.

##### Les attaques Rainbow Table
**Le problème** : si deux utilisateurs ont le même mot de passe (ex.:abc123)

Ils auront exactement le même hash.

Un attaquant peut :
-Pré-calculer des millions de mots de passe communs
Stocker leurs hashes
Comparer avec les hashes volés

C’est ce qu’on appelle une **Rainbow Table Attack**.

**La solution : un SALT**
Pour éviter cela, on ajoute un salt.

Un salt est :
- Une chaîne aléatoire
- Générée pour chaque utilisateur
- Unique

Exemple :
```ts
password = "abc123"
salt = "X9aT7"
```

On ne hash pas juste le mot de passe :
```ts
hash(password + salt)
```

Puis on stocke : `salt + hash`

Chaque utilisateur aura donc un hash différent,
même avec le même mot de passe.

##### Comment fonctionne le login avec un salt ?
Lors du **signup**

1. Génération d’un salt aléatoire
2. Calcul du *hash*(password + salt)
3. Stockage `salt.hash`

Lors du **signin** :
1. On récupère l’utilisateur via email
2. On récupère : le salt + le *hash* stocké
3. On refait : `hash(password_reçu + salt_stocké)`
4. Comparaison avec le *hash* stocké en base 
Si les valeurs sont identiques → accès autorisé.

##### En quoi le rainbow attack devient inefficace ?
Sans salt :
- Un seul calcul par mot de passe
- *Hash* identique pour tous les utilisateurs

Avec salt :
- Chaque utilisateur a un *hash* différent
- L’attaquant doit recalculer pour chaque salt
- Impossible d’utiliser une table pré-calculée
- Le coût devient exponentiellement plus élevé
- L’attaque devient trop lente et trop coûteuse.

<!-- ## En résumé
| Concept     | Rôle                                          |
| ----------- | --------------------------------------------- |
| Hash        | Transformation irréversible d’un mot de passe |
| Comparaison | Vérification *hash* vs *hash*                 |
| Salt        | Protection contre les rainbow tables          |
| Cookie      | Identification de l’utilisateur               | --> |
