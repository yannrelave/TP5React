# TP Routage avec React

Pour bien comprendre ce qui se trame dans le routage, nous ré-implémentons les composants vus en cours à l'aide de fonctions primitives fournies par la libraire `react-dom`.


## Ré-implémenter le composant `Link`

Notre version de `Link`, appelée `CustomLink` accepte un props `to` sous la forme d'une chaîne de caractères. Lorsqu'un utilisateur clique sur `Link`, il est redirigé vers la page indiquée par `to`.

**1. Comment fait-on une redirection avec `react-router` ?**

Pour faire une redirection avec `react-router` il faut aller dans l'historique, présent dans les props, puis utiliser la méthode `push()`. Passer en paramètre la page dans laquelle vous voulez rediriger l'utilisateur.

Mais comment avoir accès à cet objet que vous venez d'évoquer ? Dans le cours, il était injecté par `Route`. Cette fois-ci, nous allons utiliser la fonction [`useHistory`](https://reactrouter.com/web/api/Hooks/usehistory). 

**2. Après avoir lu la documentation correspondante, décrivez le fonctionnement de cette fonction.**

Pour avoir accès à l'historique nous pouvons utiliser un "hook", ce qui rend plus facile l'accéssibilité de cette fonction. Cette fonction permet d'aller chercher l'historique des pages visitées par l'utilisateur. Il faut ensuite utilisé la méthode `push("/NomDeLaPage")` et renseigner le chemin de la page pour faire la redirection.

**3. En utilisant cette fonction, devez-vous implémenter le `CustomLink` composant sous la forme d'une fonction ou d'une classe ?**

Il faut implémenter le `CustomLink` sous la forme d'une fonction.

**4. Faites l'implémentation de `CustomLink`, ajoutez les `propTypes`, testez la dans une codesandbox et copiez votre implémentation de `CustomLink` dans ce document.**

```javascript
import React, { Fragment } from "react";
import {
  BrowserRouter,
  Link,
  Route,
  Switch,
  useRouteMatch
} from "react-router-dom";
import "./styles.css";

export default function App() {
  return (
    <BrowserRouter>
      <main>
        <nav>
          <ul>
            <li>
              <FormeMenuLink to="/" label="Home">
                Home
              </FormeMenuLink>
            </li>
            <li>
              <FormeMenuLink to="/about" label="About">
                About
              </FormeMenuLink>
            </li>
            <li>
              <FormeMenuLink to="/contact/" label="Contact">
                Contact
              </FormeMenuLink>
            </li>
          </ul>
        </nav>
      </main>
      <Switch>
        <Route exact={true} path="/">
          <Home />
        </Route>
        <Route path="/contact/">
          <Contact />
        </Route>
        <Route path="/about">
          <About />
        </Route>
      </Switch>
    </BrowserRouter>
  );
}

function FormeMenuLink({ label, to }) {
  return <Link to={to}>{label}</Link>;
}

// Home Page
const Home = () => (
  <>
    <h1>Home</h1>
    <FakeText />
  </>
);
// About Page
const About = () => (
  <>
    <h1>About</h1>
    <FakeText />
  </>
);
// Contact Page
const Contact = () => (
  <>
    <h1>Contact</h1>
    <FakeText />
  </>
);
const FakeText = () => (
  <p>
    Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod
    tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam,
    quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo
    consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse
    cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat
    non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
  </p>
);

```

Pour procéder à l'implémentation de `CustomLink` sous l'autre forme (classe ou fonction, selon votre réponse à la question 3.), nous utiliserons la fonction [`withRouter`](https://reactrouter.com/web/api/withRouter). Il s'agit d'un HOC ; nous les verrons en détail dans un prochain cours.

**5. Reprenez les questions 2 à 4 avec `withRouter`**

`withRouter` permet de stocker dans un component les "props" : les "match", la location, ainsi que l'historique des pages visité par l'utilisateurs.
`withRouter` doit être implémenté sous la forme d'une classe.


## Ré-implémenter le composant Route

Notre composant `CustomRoute` accepte deux props: 

 - `path` est une chaîne de caractères ;
 - `component` est un composant à afficher lorsque la localisation correspond au `path`. Ce composant reçoit en props `history`, `location` et `match`.

**6. Dans la documentation de la `react-router`, trouvez trois hooks permettant d'obtenir les variables `history`, `location` et `match`.**

Pour obtenir la variable `history` il faut utiliser le "hooks" `useHistory`. `useLocation` est le "hooks pour obtenir la variable `location`. Pour récupérer la variable `match` il faut utiliser le "hooks" `useRouteMatch`.

**7. Faites l'implémentation de `CustomRoute`, ajoutez les `propTypes`, testez la dans une codesandbox et copiez votre implémentation de `CustomRoute` dans ce document.**


## Tester le routage avec React

Pour tester le routage, nous cherchons une manière simple de contrôler la localisation de la page.
Puisque `BrowserRouter` utilise l'URL du navigateur, nous préférons utiliser [`MemoryRouter`](https://reactrouter.com/web/api/MemoryRouter), qui peut être contrôlé à partir de son props `initialEntries`. 

Nous séparons alors l'appel au `Router` du reste de l'application afin de simplifier la mise en test de cette dernière:


```jsx
// index.js
export default function App() {
  return (
      <main>
        <nav>
          <ul>
           <li>
              <Link to="/pages/mon-premier-article">Mon premier article</Link>
            </li>
            <li>
              <Link to="/pages/mon-second-article">Mon second article</Link>
            </li>
          </ul>
        </nav>
      <Route path="/post/:name">
        <Post />
      </Route>
      </main>
  );
}
// Post Page
const Post = () => {
  let { name } = useParams();
  return <h1>{name.replaceAll("-", " ")}</h1>;
};


const rootElement = document.getElementById("root");
ReactDOM.render(
  <React.StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </React.StrictMode>
  rootElement
);
```

**8. Ce code utilise `useParams`, que nous n'avons encore jamais utilisé. A quoi sert-il ?**

`useParams` permet de récupérer les valeur passer en paramètres de l'URL de la route "name" sous la forme clé/valeur.

**9. Créez un nouveau projet à partir de code.  Créez un test qui injecte `MemoryRouter` autour de `App`. Indiquez des URL à `MemoryRouter` et vérifiez que les posts affichent bien le contenu de l'URL.**

Aide : la [documentation](https://reactrouter.com/web/guides/testing) fournit quelques renseignements sur le testing d'une application avec un routeur. Vous pouvez reprendre également la [documentation de React conernant les tests](https://reactjs.org/docs/testing.html).



