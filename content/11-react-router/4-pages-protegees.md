+++
pre = '<b>4. </b>'
title = "Routes protégées"
weight = 16
draft = false
+++

Dans certaines cas, on voudrait que certaines pages de notre application ne puissent être accessible qu'à un utilisateur connecté (par exemple la page `Dashboard`). Avec React en *Data Mode*, on peut tout à fait le faire en mettant en place un "garde" (*Guard*) qui s'exécute au niveau du *loader* avant d'afficher la page.

## Fonction utilitaire requireAuth
Cette fonction est un *middleware* côté client : elle vérifie si l'utilisateur est connecté et, sinon, le redirige intelligemment.

```jsx
// services/auth.js
import { redirect } from "react-router-dom"
import { getCurrentUser } from "../services/api"

export async function requireAuth(request) {  
  try {
    // 1. On demande au backend NestJS : "Qui suis-je ?" 
    // Si le cookie de session est invalide, l'API lancera une erreur 401
    return await getCurrentUser()
  } catch (error) {
    // 3. Si non autorisé, on redirige vers /login pour qu'il se connecte
    throw redirect(`/login`)
  }
}
```

## Utilisation dans les Loaders
Pour protéger une route, il suffit d'appeler `requireAuth` à la première ligne de son loader. Si la fonction "throw" une redirection, le reste du loader ne s'exécutera jamais.

**Exemple avec `host-dashboard.loader.js` :**
```jsx
import { getHostVans } from "/src/services/api"
import { requireAuth } from "/src/services/auth"

export async function hostDashboardLoader({ request }) {
  // ÉTAPE 1 : Le garde vérifie l'accès
  const user = await requireAuth(request)
  
  // ÉTAPE 2 : On ne récupère les données privées QUE si l'étape 1 a réussi
  const vans = getHostVans(user.id)
  return { vans, user }
} 
```

## authLoader : Garde sans récupération de données
Parfois, vous avez des routes qui doivent être sécurisées, mais qui n'ont pas à récupérer des données. Au lieu de réécrire la logique à chaque fois, on crée un `authLoader` réutilisable.

### Fonction authLoader

C'est un *loader* minimaliste. Son seul rôle est de servir de "garde" en appelant notre fonction `requireAuth`.

```jsx
// loaders/auth.loader.js
import { requireAuth } from "/src/services/auth"

export async function authLoader({ request }) {
  // On attend que requireAuth vérifie la session auprès de NestJS
  // Si l'utilisateur n'est pas connecté, requireAuth jettera (throw) une redirection
  return await requireAuth(request)
}
```

### Utilisation dans App.jsx
L'avantage est majeur : vous pouvez protéger n'importe quelle route en une seule ligne, sans créer de fichier *loader* spécifique pour chaque page.

```jsx
// App.jsx
import { authLoader } from "./loaders/auth.loader.js"
  <Route
    path="income"
    element={<Income />}
    loader={authLoader}
  />

  <Route
    path="reviews"
    element={<Reviews />}
    loader={authLoader}
  />
```