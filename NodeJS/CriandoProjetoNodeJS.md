# Criando um projeto NodeJS com Typescript

1. `yarn init -y`
2. `yarn add express`
3. `yarn add typescript @types/express -D`
4. `yarn tsc --init`
5. Em `tsconfig.json`:
   1. rootDir: "./src"
   2. outDir: "./dist"
6. `yarn add ts-node-dev -D` (Serve para execução de Typescript em NodeJS)

Em `package.json` inserir os scripts:

```JSON
"scripts": {
"build": "tsc",
"dev:server": "ts-node-dev --transpile-only --ignore-watch node_modules src/server.ts"
}
```

* --transpile-only: Não verifica se o código está certo ou não, para ser mais rápido
* --ignore-watch node_modules: Ignora a pasta node_modules

Em `src/server.ts` para testar inserimos:

```typescript
import express from 'express';

const app = express();

app.get('/', (request, response)=>{
        return response.json({message: "Working"});
})

app.listen(3333, () => console.log("🚀 Server started on port 3333"));
```
