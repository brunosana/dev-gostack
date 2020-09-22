# Styled Components

Controlar a estilização da aplicação de forma não-global é um dos maiores desafios em aplicações. Pois algumas vezes, um CSS pode estar integrado com outros componentes e isso seria um problema muito grande

Podemos começar instalando com `yarn add styled-components` e `yarn add @types/styled-components -D`.

Criamos o nosso style em `pages/Dashboard/style.ts`:

```typescript
import styled from 'styled-components';

export const Title = styled.h1`
    font-size: 48px;
    color: #3A3A3A;
`;
```
Agora, na página `pages/Dashboard/index.tsx`:

```typescript
import React from 'react';
import { Title } from './styles'

const Dashboard: React.FC = () => {
    return <Title>Explorer de Repositórios GitHub</Title>
}

export default Dashboard;
```

Usamos o componente exportado como uma tag, assim ele só será renderizado nela.

## Global Styled Components

Há alguns casos onde queremos que o estilo seja utilizado de forma global, para isso, é interessante que criemos um arquivo (por ex.) `src/styles/global.ts`:

```typescript
import { createGlobalStyle } from 'styled-components';
import githubBackground from '../assets/github_background.svg';

export default createGlobalStyle`
    * {
        margin: 0;
        padding: 0;
        outline: 0;
        box-sizing: border-box;
    }

    body{
        background: #F0F0F5 url(${githubBackground}) no-repeat 70% top;
        -webkit-font-smoothing: antialiased;
    }

    border-radius, input, button{
        font: 16px Roboto, sans-serif;
    }

    #root{
        max-width: 960px;
        margin: 0 auto;
        padding: 40px 20px;
    }

    button {
        cursor: pointer;
    }

`;
```

PS: Para um globalstyle, importamos um componente específico do `styled-components` chamado `createGlobalStyle` e exportamos ele como `default`.

Nesse exemplo, joguei uma imagem em `src/assets/github_background.svg` para ser utilizada.

Criamos o código CSS e importamos como uma Tag isolada em `App.js`:

```typescript
import React from 'react';
import Routes from './routes';
import { BrowserRouter } from 'react-router-dom';
import GlobalStyle from './styles/global'

const App: React.FC = () => (
    <>
    <BrowserRouter>
        <Routes />
    </BrowserRouter>
    <GlobalStyle />
    </>
);

export default App;
```

Colocamos após o `BrowserRouter` com o `fragment`.