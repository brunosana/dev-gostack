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