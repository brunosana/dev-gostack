# Rotas com ReactJS

Para lidar com as rotas na web, instalamos uma lib com `yarn add react-router-dom` e `yarn add @types/react-router-dom -D`.

Para aplicar em um projeto corretamente, suponha a seguinte estrutura:

```
- src
  - pages
    - Dashboard
      - index.tsx
    - Repository
      - index.tsx
  - routes
    - index.tsx
  - App.tsx
  [...]
```

Onde `pages` irá conter as páginas da aplicação e `routes` irá controlar as rotas.

Em `Repository/index.tsx` temos:

```javascript
import React from 'react';

const Repository: React.FC = () =>{
    return <h1>Repository</h1>
}

export default Repository;
```

PS: Tipamos a função do componente como `React.FC`. FC = Function Component.

E em `Dashboard/index.tsx` temos quase que exatamente a mesma estrutura, substituindo todas as ocorrências de `Repository` para `Dashboard`.

Importamos então em `routes/index.tsx` as páginas e fazemos o redirecionamento de cada uma:

```javascript
import React from 'react';
import { Switch, Route } from 'react-router-dom';

import Dashboard from '../pages/Dashboard';
import Repository from '../pages/Repository';

const Routes: React.FC = () => {
    return (
        <Switch>
            <Route path='/' exact component={Dashboard} />
            <Route path='/repository' component={Repository} />
        </Switch>
    );
}

export default Routes;
```

* Além de importar as páginas criadas (de acordo com a estrutura anterior), importamos do `react-router-dom` as tags `Switch` e `Route`.
* **`Route`** -> Tag de endereçamento, passamos o parâmetro `path` que será a rota a ser redirecionada e o `component` que é o que será renderizado em tela. O `Route` faz uma verificação de inclusão, portanto, precisamos passar o parâmetro `exact` nela, pois caso contrário, como o path `/` é incluso em todas as rotas, ele seria sempre redirecionado para a rota raíz.
* **`Swtich`** -> É uma tag de troca de rotas. Ele garante que apenas uma rota será mostrada. Sem ele, ao entrar na rota `/repository`, iriam ser exibidas tanto a rota de path `/` quando `/repository`.

E em `App.tsx` :

```javascript
import React from 'react';
import Routes from './routes';
import { BrowserRouter } from 'react-router-dom';

const App: React.FC = () => (
    <BrowserRouter>
        <Routes />
    </BrowserRouter>
);

export default App;
```

* Importamos o nosso arquivos Routes.
* Também tipamos a função do componente `App`.
* Importamos a tag `BrowserRouter` que irá ficar por fora da tag `Routes` importada anteriormente.

## Navegando entre rotas

Suponha que você tem uma página `search` e uma página `read`. A `search` ela pesquisa um determinado conjunto de dados, e ao clicar nesse dado, é direcionado para a página `read` para informações mais detalhadas.

A tag de redirecionamento será a `Link` e podemos importá-la do `react-router-dom`. Esse acesso é feito sem recaregar a página, dando mais performance ao código (podemos deduzir que essa tag estará em um loop):

```jsx
<Link to={`read/${searchItem.id}`} key={searchItem.id}>
    <img src={searchItem.image}
    alt={searchItem.image}
    />
    <div>
        <strong>{searchItem.name}</strong>
        <p>{searchItem.description}</p>
    </div>
</Link>
```

A tag se comporta semelhante a tag `a`, apenas mudando de `href` para `to`. Veja que estamos passando um `route param` para a rota saber qual item irá ler!

No arquivo `routes.ts`, precisamos informar que o usuário irá passar um parâmetro:

```jsx
<Switch>
    <Route path='/search' exact component={Search} />
    <Route path='/read/:id' component={Read} />
</Switch>
```

E na página, para capturarmos os parâmetros passados pela rota, utilizamos um método específico, o `useRouteMatch`. Para podermos capturar os valores de forma correta, criamos uma interface `SearchParams` para passar como parâmetro do `useRouteMatch` e poder ter a intellisense do vscode habilitada para autocompletar os dados. Como estamos passando apenas o ID, vamos informar em forma de string:

```jsx
interface SearchParams{
    repository: string;
}
```

E agora podemos tipar nosso `useRouteMatch`:

```jsx
const { params } = useRouteMatch<SearchParams>();
```

Agora podemos informar o valor passado em uma tag:

```jsx
return (
    <>
        <h1>Search ID: {params.id}</h1>
    </>
);
```
