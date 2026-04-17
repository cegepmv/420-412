+++
pre = '<b>2. </b>'
title = "Récupération de données (loaders)"
weight = 14
draft = false
+++

Dans cette section, nous allons voir comment récupérer des données (requête `GET`) de notre backend et les afficher dans notre frontend. 

## Création d'un service API
La première étape est d'implémenter les fonctions `fetch` qui nous permettront de faire les requêtes nécessaires aux backend pour récupérer les données.

Plutôt que d'écrire des **fetch** éparpillés partout, une bonne pratique est de créer un service d'API qui contient toutes les fonctions.

**Contenu du fichier /services/api.js :**
```jsx
// API URL
const BASE_URL = import.meta.env.VITE_API_URL || "http://localhost:3000/api"

// Fonction "squelette" pour faire des requêtes à l'API
async function request(path, options = {}) {

  const res = await fetch(`${BASE_URL}${path}`, {

    credentials: "include", // ESSENTIEL : Envoie les cookies de session au backend NestJS
    headers: {
      "Content-Type": "application/json",
    },
    ...options
  })

  // Tenter de parser la réponse en JSON, ou retourner un objet vide si ça échoue
  const data = await res.json().catch(() => ({}))

  // Si la réponse n'est pas OK, on rejette la promesse avec un objet d'erreur
  if (!res.ok) {
    throw {
      message: data.message ?? "Request failed",
      statusText: res.statusText,
      status: res.status
    }
  }
  return data
}

// Fonctions prêtes à l'emploi pour plus tard : 
export const getVans = async () => await request("/vans")
export const getSingleVan = async (id) => await request(`/vans/${id}`)
export const getHostVans = async (userId) => await request(`/users/${userId}/vans`)

export const loginUser = async (creds) => (
  await request("/auth/signin", {
    method: "POST",
    body: JSON.stringify(creds)
  }))

export const registerUser = async (creds) => (
  await request("/auth/signup", {
    method: "POST",
    body: JSON.stringify(creds)
  }))

export const getCurrentUser = async () => {
  return await request("/auth/whoami")
}

export const logoutUser = async () => {
  return await request("/auth/signout", {
    method: "POST"
  })
}
```

## Loaders
Dans le code actuel, on passe les données via des props depuis `App.jsx` :
```jsx
<Route path="/" element={<MainLayout user={user} vans={vans}/>}>
```
Nous allons supprimer cet état centralisé pour **rendre chaque route autonome** grâce aux **Loaders**. 

Un **loader** est une fonction JavaScript qui s'exécute côté client, mais **avant le rendu du composant**.

**Exemple pour la fonction `loaders/vans.loader.js` :**
```jsx
import { getVans } from "/src/services/api"

// Cette fonction s'exécute AVANT le rendu
export async function vansLoader() {
  return { vans: await getVans() }
}
```

**Dans le composant de la page Vans :**
```jsx
export default function Vans() {
  // Récupère directement le résultat du loader
  const { vans } = useLoaderData()

  return (
    <div className="van-list-container">
      <h1>Explore our van options</h1>
        <VansList vans={vans}/>
    </div>
  )
}
```

### Connecter le Loader à la Route
Dans le fichier `App.jsx`, il faut dire à la route quel *loader* utiliser : 
```jsx
<Route
  path="vans"
  element={<Vans />}
  loader={vansLoader} // La fonction importée de loaders/vans.loader.js
/>
```

#### Pourquoi ça nous sauve la vie ?
+ **Plus de useEffect() :** On s'assure de recevoir les données **AVANT** le premier rendu, contrairement à `useEffect()` qui s'exécute **APRÈS** le rendu.
  + **Avec useEffect :** Le navigateur télécharge le JS -> Rend le composant -> Affiche un loading -> Lance le fetch -> Reçoit les données -> Re-rend le composant.
  + **Avec les Loaders :** Le navigateur voit le changement d'URL -> Lance le fetch immédiatement -> Attend la réponse -> Affiche la page directement avec les données.
+ **Plus de "Prop Drilling" :** Plus besoin de passer les données du parent à l'enfant sur 4 niveaux. Le composant demande ce dont il a besoin, et *React Router* lui sert sur un plateau.
+ **Synchronisation parfaite avec le Backend :** Si vous avez un paramètre d'URL (ex: `/vans/:id`), le *loader* reçoit l'objet `params` automatiquement.

#### Exemple pour la page VanDetail

**Fichier `loaders/van-detail.loader.js` :** 
```js
export function loader({ params }) {
  // params.id contient l'ID de l'URL
  return { van: await getVan(params.id) }
}
```

**Composant de la page VanDetail :**
```jsx
export default function VanDetail() {
  const { van }  = useLoaderData()

  return (
    <div className="van-detail-container">
      <Link className="back-button" to="/vans">
        &larr; <span>Back to vans</span>
      </Link>
      <Van van={van} />
    </div>
  )
}
```

#### Exemple pour le MainLayout
C'est ici que l'on voit la puissance du MainLayout. On veut savoir qui est l'utilisateur avant même d'afficher l'interface.

**Dans `loaders/main-layout.loader.js` :**
```jsx
import { getCurrentUser } from "/src/services/api"

export async function mainLayoutLoader() {
  try {
    // On fait une requête "whoami" au backend, 
    // si on reçoit les données de l'utilisateur, c'est qu'on est connecté
    const user = await getCurrentUser() 
    return { user: user } // L'utilisateur est connecté, on utilise ses données dans le context
  } catch (error) {
    return { user: null } // Pas de session active, mais on laisse l'app charger
  }
}
```
**Dans `layouts/MainLayout.jsx` :** 
```jsx
export default function MainLayout() {
  const { user } = useLoaderData()

  return (
    <div className="site-wrapper">
      <Header user={user} />
      <main>
        <Outlet/>
      </main>
      <Footer />
    </div>
  )
}
```

Cela nous permet de changer les boutons de la barre de navigation en fonction de si l'utilisateur est connecté ou pas.
**Dans `components/ui/Header.jsx` :**
```jsx
import { NavLink, Link, Form } from "react-router-dom"
import "./Header.css"

export default function Header({ user }) {

  return (
    <header>
      <Link className="site-logo" to="/">#VanLife</Link>
      <nav>
        <Link className="nav-button" to="/about">About</Link>
        <Link className="nav-button" to="/vans">Vans</Link>
        {/* 
          Utilisateur connecté: le bouton affiche son nom et le redirige vers son dashboard 
          Utilisateur pas connecté: affiche "Login" et le redirige vers la page de connexion
        */}
        <Link className="nav-button" to={user ? "/host" : "/login"}>
          {user ? user.name : "Login"} 
        </Link>

        {/* 
          Utilisateur connecté: Un autre bouton est disponible pour le déconnecter
        */}
        {user && <button>Logout</button> }
      </nav>
    </header>
  )
}
```

## Documentation
+ [Documentation React Router - Le hook useLoaderData](https://reactrouter.com/api/api/hooks/useLoaderData)