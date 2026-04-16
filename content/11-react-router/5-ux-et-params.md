+++
pre = '<b>5. </b>'
title = "Expérience utilisateur (UX)"
weight = 17
draft = false
+++

Dans cette section, nous allons voir comment améliorer l'expérience utilisateur, d'abord en utilisant des *Query String* (ce qui se trouve après le ? dans l'URL) pour faire circuler des informations entre deux pages, puis en explorant le composant `<NavLink>` de *React Router*. 

## Utilité 
Imaginez ce scénario :

1. L'utilisateur navigue directement sur son *Dashboard*.
2. Votre code détecte qu'il n'est pas connecté.
3. Vous le redirigez vers `/login`.

+ **Problème :** Une fois connecté, il atterrit sur l'accueil. Il a perdu sa destination initiale.
+ **La solution :** "Transporter" l'intention de l'utilisateur dans l'URL.

## 1 - Envoyer les infos (requireAuth)
On modifie notre garde pour qu'il construise une URL de redirection "enrichie".
```jsx
// services/auth.js
export async function requireAuth(request) {

  const pathname = new URL(request.url).pathname // Ex: /host/income
  
  const message = "You must log in first!" // Message à afficher sur la page login
  
  try {
    return await getCurrentUser()
  } catch (error) {
    // On "encode" la destination et un message dans l'URL de login
    throw redirect(`/login?redirectTo=${pathname}&message=${message}`)
  }
}
```

L'URL finale ressemblera à : `/login?redirectTo=/host/income&message=You%20must%20log%20in%20first`

## Étape 2 - Lire les infos (Login.jsx)
Dans la page de connexion, on utilise le hook `useSearchParams` pour extraire ces informations et les afficher.
```jsx
// pages/Login/Login.jsx
import { useSearchParams } from "react-router-dom"

export default function Login() {
  const [searchParams] = useSearchParams()
  
  // On récupère les valeurs par leur clé
  const message = searchParams.get("message")

  return (
    <div>
      {/* Si un message existe dans l'URL, on l'affiche en rouge */}
      {message && <h3 className="red-text">{message}</h3>}
      
      <Form method="post">
         {/* ... inputs ... */}
      </Form>
    </div>
  )
}
```
## Étape 3 : Utiliser la destination (loginAction)
Enfin, dans l'action de connexion, on récupère le `redirectTo` pour savoir où envoyer l'utilisateur après le succès du `loginUser`.
```jsx
async function loginAction({ request }) {
  const formData = await request.formData();

  const creds = {
    email: formData.get('email'),
    password: formData.get('password'),
  };

  // On extrait redirectTo de l'URL actuelle du formulaire. 
  // S'il elle n'existe pas, on utilise la route de son dashboard (/host)
  const pathname = new URL(request.url)
    .searchParams.get("redirectTo") || "/host"

  try {
    await loginUser(creds)
    // Succès : On redirige vers la page demandée initialement !
    return redirect(pathname)
  } catch (error) {
    if (error.status === 404 || error.status === 400) {
      return "Email or password is incorrect. Please try again."
    }
    return "Login failed. Please try again."
  }
};
```

## Exemple avec registerAction
```jsx
import { redirect } from "react-router-dom";
import { registerUser } from "/src/services/api";

export default async function registerAction({ request }) {
  const formData = await request.formData();
  const userInfo = {
    name: formData.get('name'),
    email: formData.get('email'),
    password: formData.get('password'),
  };
  try {
    await registerUser(userInfo);
    const message = "Registration successful! Please log in.";
    return redirect(`/login?message=${message}`);
  } catch (error) {
    return error.message ?
      `Registration failed (${error.message}).`
      :
      "Registration failed. Please try again."
  }

};
```

## NavLink vs Link (indiquer l'état actif)
Alors que le composant `<Link />` sert uniquement à naviguer, `<NavLink />` est une version "intelligente" qui sait si l'URL vers laquelle il pointe correspond à la page actuellement affichée par le navigateur.

`<NavLink />` met à notre disposition une propriété `isActive`. On peut l'utiliser pour appliquer dynamiquement une classe CSS (comme `.active`) afin de souligner ou mettre en gras le lien de la page courante.

**Exemple dans le Header.jsx :**
```jsx
export default function Header({ user }) {
  // On crée une fonction qui reçoit l'objet { isActive } de React Router
  // Ensuite, on ajoute une classe de style CSS (activ) qui souligne le bouton
  // Si la page sur laquelle il redirige est celle où on est
  const linkClassName = ({ isActive }) => isActive ? "nav-button active" : "nav-button"

  return (
    <header>
      <Link className="site-logo" to="/">#VanLife</Link>
      <nav>
        {/* NavLink applique automatiquement la logique isActive */}
        <NavLink className={linkClassName} to="/about">About</NavLink>
        <NavLink className={linkClassName} to="/vans">Vans</NavLink>

        {/* On utilise Link ici car ce n'est pas une destination fixe*/}
        <Link className="nav-button" to={user ? "host" : "login"}>
          {user ? user.name : "Login"}
        </Link>
      </nav>
    </header>
  )
}
```

### Cas particulier de la racine : la prop end
Dans un sous-menu (comme le `HostLayout`), le lien vers le `Dashboard` pointe souvent vers la route parente (.). Sans la propriété `end`, ce lien resterait considéré comme "actif" même si vous allez sur `/host/income`, car `/host/income` commence par `/host`.

**Exemple dans le HostLayout.jsx :**
```jsx
export default function HostLayout() {
  const linkClassName = ({ isActive }) => isActive ? "nav-button active" : "nav-button"

  return (
    <>
      <nav className="host-nav">
        {/* 'end' garantit que le lien n'est actif QUE si l'URL s'arrête exactement ici */}
        <NavLink to="." end className={linkClassName}>
          Dashboard
        </NavLink>
        
        <NavLink to="income" className={linkClassName}>
          Income
        </NavLink>
        
        <NavLink to="reviews" className={linkClassName}>
          Reviews
        </NavLink>
      </nav>
      <Outlet />
    </>
  )
}
```
**Résultat :**
+ **Feedback visuel :** L'utilisateur n'a pas besoin de regarder l'URL pour savoir dans quelle section il se trouve.
+ **Accessibilité :** Les lecteurs d'écran peuvent utiliser ces informations pour indiquer le lien courant aux utilisateurs malvoyants.
+ **Clarté des sous-menus :** Dans des structures complexes (Dashboard > Vans > Détails), NavLink permet de garder une trace visuelle de la hiérarchie.

## Documentation
+ [Documentation React Router - Le composant NavLink](https://reactrouter.com/api/components/NavLink)
+ [Documentation React Router - Le hook useSearchParams](https://reactrouter.com/api/components/Form)