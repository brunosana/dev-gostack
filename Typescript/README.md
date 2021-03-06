## Typescript Notes

### Tópicos

* Por que Typescript?
* Configurando o Projeto
* Quando adicionar tipos
* Criando tipagens

## Por que Typescript?

> Typescript é uma linguagem totalmente baseada em JS (ela utiliza JS por baixo dos panos) com tipagem e a vantagem de sempre poder acessar as novas features JS, utilizando no Node e no Browser. Também funciona como o Babel, convertendo a sintaxe mais recente em uma mais antiga e que funcione em outros navegadores.

PS

* O NodeJS e o Browser não entende Typescript. Ou seja, todo código Typescript precisa ser convertido em JS.

A vantagem de usar Typescript é que em ambiente de desenvolvimento o uso dela é essencial! Em projetos com tempo de vida maior ou que outras pessoas irão trabalhar, ter a intellisense mostrando todo o corpo do objeto facilita bastante ao garantir que todo programador que usar o projeto entenderá o código.

## Configurando o Projeto

Para iniciar um projeto em typescript, primeiro com o NodeJS, iniciamos o projeto normalmente com `yarn init -y`.

Depois, instalamos a dependência `typescript` como dependência de desenvolvimento, pois quando a aplicação estiver rodando em produção estará usando o arquivo convertido em JavaScript, portanto, usamos `yarn add typescript -D`.

Já podemos criar o arquivo em `src/index.ts` com a extensão Typescript.

Vamos simular uma API instalando o express com `yarn add express`.

Já podemos criar normalmente a API com as novas funcionalidades do JavaScript, usando import como fazemos no ReactJS e ReactNative, embora o NodeJS não tenha suporte, como estamos programando em Typescript é possível.

Porém, o IntelliSense do editor estará imperfeito, pois o arquivo não tem a extensão `.js`. O que acontece na verdade é que por baixo dos panos o Visual Code usa Typescript, então ao escrevermos nossa aplicação em JS ele adicionava todas as tipagens e conseguíamos usar o autocomplete  por conta disso. Porém, quando estamos utilizando o javascript a forma automática de usar a inteligêncioa do VSCode não acontece. Para isso, precisamos de um pacote de declaração de tipos, que é onde estará toda essa parte de de autocomplete.

A sintaxe é `yarn add @types/NOME_DO_PACOTE -D`, nesse caso seria `yarn add @types/express -D`. Usamos a dependência de desenvolvimento pois esse pacote só será necessário durante o desenvolvimento do código.

Podemos usar agora de forma automática:

```typescript
import express from 'express';
const app = express();
app.get('/', (request, response)=>{
    return response.json({message: "Working"});
});
app.listen(3333, ()=>{
    console.log("Server Started")
});
```

Perceba que estamos utilizando a sintaxe nova do JavScript (usando o import ao invés de require).

Porém, ao executar o projeto, o comando `node src/index.ts` não irá funcionar, pois o NodeJS não entende o Javascript.

Para isso usamos o package `typescript` que instalamos no começo. Precisamos de um arquivo binário `tsc` que ele gera (Arquivos binários instalados de libs podem ser vistos em `node_modules/bin`).

Naturalmente ao usar o `tsc` sem inicializar o Typescript no repositório do projeto ele não irá funcionar. Pois fica sem configurações para se basear para converter o código. Assim como no Babel, onde precisamos de um arquivo `babel.config.js`.

Portanto, para inicializar o typescript no repositório usamos `yarn tsc --init` na pasta do projeto e um arquivo `tsconfig.json` será gerado.

Inserimos no terminal `yarn tsc` para gerar um arquivo `.js` podemos usar o `node src/index.js` para executar a API.

Por boas práticas de programação, os arquivos da pasta `src` são para desenvolvimento, então é bom usar uma pasta específica para armazenar os arquivos `.js`. Para isso, no arquivo `tsconfig.json` descomentamos a linha `outDir` (por volta da linha 17) e mudamos o seu valor para `./dist`. Assim, toda vez que o comando `yarn tsc` for usado, todos os arquivos (incluindo a sua estrutura de pastas) serão convertidos e armazenados na pasta `dist`.

## Quando adicionar tipos

Suponha que na API temos uma arquivo `src/routes.ts` e que o corpo dele seja:

```typescript
export function serverWorking(request, response){
    return response.json({message: "Server Working"});
}
```

E modificamos o arquivo `src/index.ts` para:

```typescript
[...]
import { serverWorking } from './routes';
[...]
app.get('/', serverWorking);
[...]
```

Podemos ver que no arquivo `routes` as variaveis `request` e `response` ficam sublinhadas em vermelho, pois o editor não reconhece a tipagem daquelas variáveis. Portanto, caso algum colaborador precise ou queira alterar o código ele não irá saber, até você mesmo daqui a algum tempo pode esquecer.

Para adicionar tipagem é simples. Todo pacote instalado é possível importar apenas as tipagens.

Por tanto, importamos elas e usamos no arquivo `routes`:

```typescript
import { Request, Response } from 'express';
export function serverWorking(request: Request, response: Response){
    return response.json({message: "Server Working"});
}
```

Desse jeito o editor irá conseguir reconhecer com a IntelliSense.

O próprio editor avisa quando for preciso usar a tipagem.

## Criando Tipagens

Suponha a função createUser. Para esse exemplo, na criação de um usuário precisamos de um Nome, email e password, que podemos definir seus tipos como string. Como o Nome não será obrigatório para esse exemplo, caso não declarado será atribuida a string vazia pra ele e automaticamente o typescript reconhecerá seu tipo como string:

```typescript
export default function createUser(name = '', email: string, password: string){
    const user = {name, email, password}
    return user;
}
```

Por boas práticas de programação, podemos receber no parâmetro o objeto usuário e desestruturá-lo:

```typescript
export default function createUser({name = '', email: string, password: string}){
    const user = {name, email, password}
    return user;
}
```

Porém dessa forma, tipagem em Json seria o mesmo que mudar o valor da variável. Para isso, criamos um tipo para o objeto em uma variável separada!

Sempre chamamos essa tipagem de interface. **Interface basicamente é como defiminos os tipos de conjuntos de informações, geralmente um objeto.**

Definimos o objeto user (no mesmo arquivo da função) então:

```typescript
interface CreateUserData{
    name?: string;
    email: string;
    password: string;
}
```

**A interrogação na variável _name_ indica que o argumento é OPCIONAL!**

Nossa função `createUser` ficará da seguinte forma:

```typescript
export default function createUser({name, email, password}: CreateUserData){
    const user = {name, email, password}
    return user;
}
```

Em outros serviços onde essa função for invocada, a intellisense irá entender e autocompletar o código!

### Tipagem em vetores

Suponha que em uma função seja necessário passar um objeto contendo um Array de strings como parâmetro. A definição seria:

```typescript
interface ProfileData{
    name?: string;
    techs: Array<string>;
}

export default function createProfile({name, techs}: ProfileData){
    const profile = {name, techs};
    return profile;
}
```

Também podemos definir o array como `string[]`.

Suponha então que o array possa, além de receber string poderá também receber um objeto contendo o nome da tecnologia e a quantidade de projetos desenvolvidos nela. Precisamos então criar um novo tipo e atribuir ele à interface `ProfileData`:

```typescript

interface TechObject{
    name: string;
    projects: number;
}

interface ProfileData{
    name?: string;
    techs: Array<string | TechObject>;
}

export default function createProfile({name, techs}: ProfileData){
    const profile = {name, techs};
    return profile;
}
```

Usamos o pipe (`|`) pois precisamos garantir que DOIS dados possam ser inseridos nele, OU uma string OU um TechObject. Serve para mais de dois tipos também!