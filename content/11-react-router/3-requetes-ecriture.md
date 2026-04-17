+++
pre = '<b>3. </b>'
title = "Envoi de données (actions)"
weight = 15
draft = false
+++

Alors que les **Loaders** servent à récupérer des données (`GET`), les **Actions** servent à en envoyer (`POST`, `PUT`, `DELETE`). C'est la méthode "moderne" de gérer les formulaires sans `useState`.

## Formulaire de connexion

### 1. Créer la fonction loginAction
Cette fonction traite les données du formulaire et communique avec le backend.

```jsx
// actions/login.action.js
import { redirect } from "react-router-dom";
import { loginUser } from "/src/services/api";

export async function loginAction({ request }) {
  // 1. On extrait les données du formulaire envoyé par le composant <Form>
  const formData = await request.formData();

  const creds = {
    email: formData.get('email'),    // Récupère la valeur de l'input name="email"
    password: formData.get('password'), // Récupère la valeur de l'input name="password"
  };

  try {
    // 3. Appel au service API (NestJS)
    await loginUser(creds)
    
    // 4. Succès : On redirige l'utilisateur vers son dashboard
    return redirect("/host")
  } catch (error) {
    // 5. Échec : On "return" un message d'erreur au lieu de le "throw"
    // Ce message sera récupéré par le composant via useActionData()
    if (error.status === 404 || error.status === 400) {
      return "Email or password is incorrect. Please try again."
    }
    return "Login failed. Please try again."
  }
};
```
### 2. Déclarer l'Action dans les Routes
Pour que *React Router* sache quelle action exécuter lors de l'envoi du formulaire, on la branche sur la route correspondante (comme pour les *loaders*).

```jsx
// App.jsx
import { loginAction } from "./actions/loginAction"

const router = createBrowserRouter(createRoutesFromElements(
  <Route path="/" element={<MainLayout />}>
    <Route 
      path="login" 
      element={<Login />} 
      action={loginAction} // Lie l'action au formulaire de cette page
    />
  </Route>
))
```


### 3. Le composant Login.jsx
Ce composant est devenu **déclaratif** : il ne contient plus de logique de "fetch" ou de "state", il se contente de lire les informations fournies par *React Router*.
```jsx
// pages/Login/Login.jsx
import {
  useActionData,
  useNavigation,
  Form,
  Link,
  useSearchParams
} from "react-router-dom"
import "./Login.css"

export default function Login() {

  // Récupère la valeur retournée par l'Action (ex: un message d'erreur string)
  const error = useActionData()

  return (
    <div className="login-container">
      <h1>Sign in to your account</h1>
      
      {/* Affiche l'erreur renvoyée par loginAction si la connexion a échoué */}
      {error &&
        <span className="red" style={{ marginBottom: "20px" }}>
          {error}
        </span>
      }

      {/* Composant Form : Intercepte le submit et envoie les données à l'Action.
        'replace' empêche de revenir au login via le bouton "Précédent" après succès.
      */}
      <Form
        method="post"
        className="login-form"
        replace
      >
        <input
          name="email" // Utilisé par formData.get('email') dans l'action
          type="email"
          placeholder="Email address"
        />
        <input
          name="password" // Utilisé par formData.get('password') dans l'action
          type="password"
          placeholder="Password"
        />
        
        <button >Log in</button>
      </Form>

      <p className="auth-redirect">
        Don't have an account? <Link to="/register">Create one here</Link>
      </p>
    </div>
  )
}
```

##### Points clés à retenir :
+ **useActionData :** C'est le lien direct avec le return de la fonction loginAction. Si l'action retourne une chaîne de caractères, ce hook la récupère.
+ **useNavigation :** Pour éviter les doubles soumissions de formulaires. On s'en sert pour désactiver le bouton le temps que le backend réponde.
+ **Zéro useState :** Remarquez qu'on ne stocke plus l'email ou le mot de passe dans un *state*. React Router s'occupe de tout via l'objet **formData**.

## Formulaire d'inscription
{{% tabs %}}
{{% tab title="actions/register.action.js" %}}
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
    return redirect(`/login`);
  } catch (error) {
    return error.message ?
      `Registration failed (${error.message}).`
      :
      "Registration failed. Please try again."
  }
};
```
{{% /tab%}}
{{%tab title="pages/Login/Register.jsx"%}}
```jsx
export default function Register() {
  const error = useActionData()
  const navigation = useNavigation()

  return (
    <div className="login-container">
      <h1>Create your account</h1>

      {/* Message d'erreur lors de la soumission du formulaire */}
      {error && <span className="red"> {error} </span>}

      <Form
        method="post"
        className="login-form"
        replace
      >
        <input
          name="name"
          type="text"
          placeholder="Full name"
          required
        />
        <input
          name="email"
          type="email"
          placeholder="Email address"
          required
        />
        <input
          name="password"
          type="password"
          placeholder="Password"
          required
        />
        <button>Sign up</button>
      </Form>

      <p className="auth-redirect">
        Already have an account? <Link to="/login">Sign in here</Link>
      </p>
    </div>
  )
}
```
{{% /tab %}}
{{% /tabs %}}

## Particularité de l'action de déconnexion (logoutAction)
Toutes les routes n'ont pas besoin d'un element (un composant). On peut déclarer une route qui n'existe que pour exécuter une Action. C'est la méthode la plus propre pour gérer la déconnexion avec React Router.

### 1. L'Action : actions/logout.action.js
Cette fonction ne traite aucune donnée de formulaire ; elle se contente d'appeler l'API et de rediriger.

```jsx
import { redirect } from "react-router-dom";
import { logoutUser } from "/src/services/api";

export default async function logoutAction() {
  try {
    // 1. Appelle le backend NestJS pour détruire la session (supprime le cookie)
    await logoutUser()
    // 2. Redirige vers la page d'accueil une fois déconnecté
    return redirect("/")
  } catch (error) {
    console.error("Logout failed:", error)
    // En cas d'erreur, on redirige quand même pour "nettoyer" l'interface
    return redirect("/")
  }
};
```
### 2. Déclaration dans App.jsx
On déclare la route normalement, mais sans le prop `element`.
```jsx
<Route
  path="/logout"      // L'URL qui déclenchera l'action
  action={logoutAction} // La logique à exécuter
/>
```
### 3. Utilisation dans le Header.jsx
Pour déclencher cette action, on utilise un composant `<Form>`. L'astuce est de lui donner l'attribut `action="/logout"` pour qu'il sache quelle route viser, même si on n'est pas sur la page de *logout*.
```jsx
// components/ui/Header.jsx
import { Form } from "react-router-dom"

// ... dans le JSX du Header
{user && (
  /* On utilise un Form avec la méthode POST pour des raisons de sécurité */
  <Form method="post" action="/logout">
    <button className="logout-button">
      Logout
    </button>
  </Form>
)}
```
#### Pourquoi ne pas faire un simple bouton avec un onClick ?

+ **Synchronisation automatique :** Dès que l'action `logoutAction` se termine et redirige vers `/`, React Router sait qu'une **"mutation"** a eu lieu. Il va automatiquement **ré-exécuter tous les Loaders actifs** (comme celui du `MainLayout` qui vérifie l'utilisateur). 
+ **Résultat :** Le state `user` passe à `null` instantanément partout dans l'application sans que vous ayez à gérer un rafraîchissement manuel.

## Documentation
+ [Documentation React Router - Le hook useActionData](https://reactrouter.com/api/components/Form)
+ [Documentation React Router - Le composant Form](https://reactrouter.com/api/components/Form)
