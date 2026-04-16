+++
pre = '<b>1. </b>'
title = "Routes et layouts"
weight = 13
draft = false
+++
--------------
Dans le code actuel, chaque page est indépendante. Si vous regardez le composant `<App />`, vous remarquez que le `<Header />` et le `<Footer />` sont "codés en dur" autour des routes. C'est fonctionnel, mais pas idéal.

## Route Parente (*Layout*)
L'idée est de traiter une route non pas comme une simple page, mais comme une **enveloppe**. Un *Layout* est un composant qui définit la structure commune (ex: Barre de navigation, Sidebar, Footer) et laisse un espace vide pour le contenu spécifique à la page.

## Composant *Outlet*
Dans un composant *Layout*, l'`<Outlet />` indique exactement où les routes enfants doivent être injectées.

**Exemple de `MainLayout.jsx` :**
```jsx
export default function MainLayout( {user, vans} ) {

  return (
    <div className="site-wrapper">
      <Header user={user}/>
      <main>
        <Outlet /> {/* Les pages Home, About, Vans, etc. s'afficheront ici */}
      </main>
      <Footer />
    </div>
  )
}
```

**Exemple de `HostLayout.jsx` :**
```jsx
import { Link, Outlet } from "react-router-dom";
import "./HostLayout.css"

export default function HostLayout() {

  return (
    <>
      <nav className="host-nav">
        <Link to="." className="nav-button">
          Dashboard
        </Link>
        <Link to="/host/income" className="nav-button">
          Income
        </Link>
        <Link to="/host/reviews" className="nav-button">
          Reviews
        </Link>
      </nav>

      <Outlet /> {/* Les pages Dashboard, Reviews et Income s'afficheront ici */}
    </>
  )
}
```

## Transition vers *createBrowserRouter*
Pour utiliser les fonctionnalités avancées de *React Router* (*Loaders*, *Actions*), nous devons abandonner la syntaxe de déclaration dans le `return` (JSX simple) pour une configuration d'objet via la fonction `createBrowserRouter()`.

Voici comment nous transformons votre structure à plat en une structure hiérarchique :

**Avant (Plat) :**
```jsx
  <Routes>
    <Route path="/" element={<Home />} />
    <Route path="/login" element={<Login />} />
    <Route path="/register" element={<Register />} />
    <Route path="/about" element={<About />} />
    <Route path="/vans" element={<Vans vans={vans} />} />
    <Route path="/vans/:id" element={<VanDetail />} />
    <Route
      path="/host/dashboard"
      element={<Dashboard hostVans={hostVans} user={user} />}
    />
    <Route path="/host/income" element={<Income />} />
    <Route path="/host/reviews" element={<Reviews />} />
    <Route path="*" element={<NotFound />} />
  </Routes>
```

**Après (Imbriqué) :**
```jsx
function App() {

  const router = createBrowserRouter(createRoutesFromElements(
    <Route path="/" element={<MainLayout user={user} vans={vans}/>} >
      <Route index element={<Home />} />
      <Route path="login" element={<Login />}/>
      <Route path="logout" />
      <Route path="register" element={<Register />}/>
      <Route path="about" element={<About />} />
      <Route path="vans" element={<Vans />} />
      <Route path="vans/:id" element={<VanDetail />}/>

      <Route path="/host" element={<HostLayout hostVans={hostVans} user={user}/>}>
        <Route index element={<Dashboard />}/>
        <Route path="income" element={<Income />}/>
        <Route path="reviews" element={<Reviews />}/>
      </Route>

      <Route path="*" element={<NotFound />} />
    </Route >
  ))

  return <RouterProvider router={router} />
}
export default App
```

## Outlet Context
Parfois, une route parente (comme un *Layout*) possède des données que ses enfants ont aussi besoin d'utiliser. Plutôt que de refaire un appel API dans chaque enfant, on peut utiliser le **Outlet Context**.

### Passer la donnée (dans le *Layout*)
Dans le composant parent, on ajoute la *prop* `context` au composant `<Outlet />`.
```jsx
import { Outlet } from "react-router-dom"

export default function MainLayout( { vans, user } ) {

  return (
    <div className="site-wrapper">
      <Header user={user}/>
      <main>
        {/* On partage les vans avec tous les enfants via le context */}
        <Outlet context={{vans: vans}}/> 
      </main>
      <Footer />
    </div>
  )
}
```

### Récupérer la donnée (dans l'enfant)
N'importe quel composant rendu à l'intérieur de cet **Outlet** peut accéder aux données grâce au hook **useOutletContext()**.

**Exemple avec la page Vans:**
```jsx
import "./Vans.css"
import { useSearchParams } from "react-router-dom"
import VansList from "/src/components/VansList"


export default function Vans() {
  // On récupère exactement ce qui a été passé dans la prop context
  const { vans } = useOutletContext()
  
  return (
    <div className="van-list-container">
      <h1>Explore our van options</h1>
      <VansList vans={vans} />
    </div>
  )
}
```

## Documentation
+ [Documentation React Router - Le composant Outlet](https://reactrouter.com/api/components/Outlet)
+ [Documentation React Router - Le hook useOutletContext](https://reactrouter.com/api/hooks/useOutletContext)