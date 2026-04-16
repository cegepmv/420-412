+++
pre = '<b>6. </b>'
title = "Pour aller plus loin"
weight = 18
draft = false
+++
---------------

## Gestion des états de chargement et des erreurs
Dans une application classique, si une API plante, l'écran devient souvent blanc ou reste bloqué. Avec React Router, on peut définir des "filets de sécurité" à différents niveaux de l'arborescence.

### Le HydrateFallback (chargement initial)
Lorsque l'utilisateur arrive sur l'application pour la première fois et que le premier *loader* prend du temps, le **HydrateFallback** permet d'afficher un indicateur de chargement global pendant que l'application "s'hydrate" (se charge en mémoire).
```jsx
  <Route path="/" 
    element={<MainLayout />} 
    HydrateFallback={ () => (
      <div className="loading-screen">
        <Spinner />
        <p>Chargement de l'application...</p>
      </div>
    )}
  />
```




### Le errorElement (Gestion des crashs et 404)
Si un *loader* échoue (ex: erreur 500 du backend) ou si un composant plante, *React Router* "remonte" l'erreur jusqu'au *errorElement* le plus proche. Cela permet de garder le reste de l'interface (comme la barre de navigation) fonctionnel.

**Création d'un composant d'erreur générique :**
```jsx
import { useRouteError, isRouteErrorResponse, Link } from "react-router-dom";

export default function ErrorPage() {
  const error = useRouteError();

  return (
    <div className="error-container">
      <h1>Oups ! Une erreur est survenue.</h1>
      <p>
        {isRouteErrorResponse(error) 
          ? `${error.status} ${error.statusText}` 
          : error.message || "Erreur inconnue"}
      </p>
      <Link replace to="/">Retourner à l'accueil</Link>
    </div>
  );
}
```

**Déclaration dans la route parente :**
```jsx
<Route path="/"
  element={<MainLayout />}
  errorElement={<Error />}
  loader={mainLayoutLoader}
>
```

## Validation de formulaires

### react-hook-form et useFetcher

+ `react-hook-form` est une librairie qui permet de gérer efficacement les states d'un formulaire. Elle permet, entre autre, de faire de la validation et d'afficher en direct si une erreur a été faite lors du remplissage du formulaire (avant de faire une requête au backend).

+ `useFetcher` est un hook puissant de *React Router* qui permet (entre autres) de soumettre le formulaire sans navigation (l'utilisateur reste sur la page s'il y a une erreur).

**Exemple d'utilisation des Actions avec useFetcher et react-hook-form pour la validation de données:** 
```jsx
import { useForm } from 'react-hook-form';
import { useFetcher } from 'react-router-dom';

export default function Register() {
  const fetcher = useFetcher();
  const { register, handleSubmit, formState: { errors } } = useForm({
    mode: "onTouched" // Valide dès que l'utilisateur quitte un champ
  });

  // Soumission vers l'action de React Router
  const onSubmit = (data) => {
    fetcher.submit(data, { method: "post" });
  };

  return (
    <div className="auth-form">
      <h1>Inscription</h1>
      
      {/* Affichage de l'erreur retournée par l'Action (Backend) */}
      {fetcher.data?.error && <p className="error-banner">{fetcher.data.error}</p>}

      <form onSubmit={handleSubmit(onSubmit)}>
        {/* Username */}
        <label>Nom d'utilisateur</label>
        <input {...register("username", { required: "Requis" })} />
        {errors.username && <span>{errors.username.message}</span>}

        {/* Email */}
        <label>Email</label>
        <input {...register("email", { 
          required: "Email requis",
          pattern: { value: /^\S+@\S+$/i, message: "Email invalide" }
        })} />
        {errors.email && <span>{errors.email.message}</span>}

        {/* Password avec RegEx complexe */}
        <label>Mot de passe</label>
        <input type="password" {...register("password", { 
          required: "Mot de passe requis",
          minLength: { value: 8, message: "Minimum 8 caractères" },
          validate: {
            hasUpper: v => /[A-Z]/.test(v) || "Doit contenir une majuscule",
            hasLower: v => /[a-z]/.test(v) || "Doit contenir une minuscule",
            hasSpecial: v => /[\W_]/.test(v) || "Doit contenir un caractère spécial"
          }
        })} />
        {errors.password && <span>{errors.password.message}</span>}

        <button type="submit" disabled={fetcher.state === "submitting"}>
          {fetcher.state === "submitting" ? "Création..." : "S'inscrire"}
        </button>
      </form>
    </div>
  );
}
```

##### Pourquoi valider côté Frontend ET Backend ?
1. **Côté Client (Frontend) :** 
    + **Réactivité :** L'utilisateur voit l'erreur instantanément sans attendre un aller-retour serveur (latence).
    + **Économie :** On ne surcharge pas le serveur avec des requêtes vouées à l'échec (ex: mot de passe trop court).
    + **Guidage :** On aide l'utilisateur à remplir correctement le formulaire pas à pas.
2. **Côté Serveur (Backend) :**
    + **Sécurité :** Un utilisateur malveillant peut facilement contourner la validation React (via Postman ou en désactivant le JS). La validation backend est obligatoire.
    + **Intégrité :** Vérifier l'unicité des données (ex: "cet email existe déjà") ne peut se faire que côté serveur avec la base de données.

### Documentation utile
+ [Validations avec useFetcher et React Hook Form](https://dev.to/kevinccbsg/react-router-data-mode-part-8-validations-usefetcher-and-react-hook-form-101k)
+ [Documentation React Router - Le hook useFetcher](https://reactrouter.com/api/hooks/useFetcher)
+ [Documentation De react-hook-form](https://react-hook-form.com/docs/useform)
+ [Alternative pour la validation : zod](https://zod.dev/)
+ [Documentation React Router - Gestion des actions](https://reactrouter.com/7.14.1/start/data/actions)
+ [Documentation React Router - Comment naviguer (NavLink, Link, Form, redirect et useNavigate)](https://reactrouter.com/7.14.1/start/framework/navigating)
