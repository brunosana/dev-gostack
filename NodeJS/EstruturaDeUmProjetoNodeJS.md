# Estrutura de um projeto Node!

## Estrutura de pastas:

```
- BackEndProjectFolder
  - node_modules
  - src
    - __tests__
    - models
    - repositories
    - routes
      - intex.ts
    - services
    - app.ts
    - server.ts
  .editorconfig
  .eslintignore
  .eslintrc.json
  .gitignore
  jest.config.js
  nodemon.json
  package.json
  prettier.config.js
  tsconfig.json
  yarn-error.log
  yarn.lock
```

A estrutura de pastas é feita dessa forma.

* routes: onde ficam as rotas da aplicação
* services: arquivos de conexão com Banco de Dados, serviços externos etc.
* models: estrutura de tipagens de arquigos (Feitos em classes)
* repositories: arquivos com conexão do banco de dados

`app.ts`:

```typescript
import express from 'express';
import routes from './routes';

const app = express();
app.use(express.json());
app.use(routes);

export default app;
```

`server.ts`:

```typescript
import app from './app';

app.listen(3333, () => {
  console.log('🚀 Server started on port 3333!');
});
```