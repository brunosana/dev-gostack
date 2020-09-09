## NodeJS Notes

### Tópicos:

* NodeJS
* NPM e Yarn
* Arquitetura e Características do Node
* CallStack
* API Rest
* Fluxo de requisição e resposta de um servidor
* Métodos HTTP
* Vantagens da API REST
* HTTP Codes
* Criando um projeto NodeJS
* Nodemon
* Usando Métodos HTTP
* Tipos de Parâmetros
* Aplicação Funcional

## NodeJS

> Node é uma ferramenta que permite o uso do Javascript no BackEnd, que é onde ficam as regras de negócio, serviços externos (como API de pagamentos, Nota Fiscal etc), conexão com o Banco de Dados etc.

PS:

* **NodeJS não lida com eventos do usuário final** | O NodeJS não faz a interação por exemplo com o clique de botões, animação quando o mouse passa sobre algum elemento (que é como o javascript trabalha no FrontEnd). Os eventos são tratados pelas **Rotas de aplicação**.
* **NodeJS NÃO é uma linguagem, é uma Plataforma de desenvolvimento BackEnd**.
* **NodeJS é construido em cima da _V8_** | V8 é a máquina que roda por trás do Chrome (ele precisa de uma engine/motor para interpretar o Javascript e produzir uma resposta).
* NodeJS é comparável com -> PHP / Ruby / Python etc.
* Nodemon

---

## NPM - Node Package Manager

> NPM ou Node Package Manager é uma plataforma que permite a instalação de LIBs de terceiros.

PS:

* Também é possível fornecer bibliotecas com o NPM. Para que terceiros utilizem a aplicação
* **Vamos utilizar o YARN xD**. O Yarn tem o mesmo conceito e função do NPM, porém é mais rápido e avançando mais rápido, com mais funcionalidades.
* NPM/Yarn é semelhante ao:
  * Composer do PHP.
  * Gems do Ruby.
  * PIP do Python.

---

## Arquitetura e Características do Node

> NodeJS tem a arquitetura **EVENT LOOP**, totalmente baseada em eventos onde o ponto central é a **Call Stack**, que é uma PILHA de eventos (que normalmene é uma função disparada pelo código) tratados no EVENT LOOP que executa em formato de pilha.

PS:

* **NodeJS é single-thread** | Executa em apenas _uma thread_ do processador, independente da quantidade de Cores, **POREM:**
* **NodeJS usa uma lib por trás em C++ chamada libuv, permitindo o uso de multithread do processamento, ou seja, a lib usa outros cores do processador para agir de forma mais rápida processando a Call Stack**.
* **NodeJS tem uma arquitetura Non-Blocking I/O**. Quer dizer que quando uma requisição é feita ao Node, não é necessário retornar todos os dados de uma só vez, é possível retornar por partes **SEM PERDER A CONEXÃO**. Algo que não acontece por exemplo no _PHP_, pois uma vez que a consulta é feita e o HTML é gerado a conexão com o Banco de Dados é perdida. Essa vantagem faz com que seja possível conexões em tempo real (Ex. web chats).

### CallStack

* Quando uma função é invocada ela entra na CallStack, e conforme outras forem executadas, elas também vão entrando em formato de _Pilha_.
* O Event Loop monitora a callstack e pega função por função e as executa com os multithreads usando a libuv com o C++ por trás.
* Como é uma PILHA, a função que vier por último será a primeira a ser executada. Esse padrão é chamadode LIFO (Last in First Out).

---

## API

### Fluxo de requisição e resposta de um servidor

1. Uma requisição e feita por um cliente (cliente = browser acessando uma URL via AJAX). Uma requisição AJAX é uma requisição feita por dentro do JS do browser.
2. Ao chegar no BackEnd, uma resposta é retornada em uma estrutura de dados. (Ex. uma requisição de busca de usuários, o BackEnd vai no DB, busca os usuários, forma um vetor com eles e retorna para o cliente em um formato)
3. O cliente recebe e pocessa o resultado (Que é diferente do Full MVC)

> No Full MVC, o BackEnd (PhP ou Ruby) retorna o HTML com os dados já renderizados. Na API, a única coisa que o BackEnd retorna é uma estrutura com os dados que o FrontEnd precisa e ele que se responsabiliza em montar a interface (Seria o ReactNative, Vue, Angular etc).

### Métodos HTTP

> As Rotas utilizam métodos HTTP. São divididos no TIPO, RECURSO ou ROTA e PARAMETROS

Principais tipos de métodos HTTP:

* GET - Quando queremos buscar algum dado no BackEnd
* POST - Criar alguma informação
* PUT - Alterar alguma informação
* DELETE - Deletar uma Informação

Exs:

* `GET` : `http://donelistapi.com/users`. O método GET, usando a URL onde a API está hospedada, _com a rota **/users**_ (Vem depois do endereço da API) está buscando todos os usuários registrados.
* `GET` : `http://donelistapi.com/users/2`. O método GET, usando a URL onde a API está hospedada, _com a rota **/users**_ (Vem depois do endereço da API) e _com o parâmetro **/1**_ (Geralmente usado depois de uma rota, especificamos o que parâmetro e rota ao escrever as linhas de código) está buscando **um usuário específico com o ID = 1**.

Para entender melhor: http://donelistapi.com/company/1/users?page=True

URL da Rota | Route | Route Params | Query Params
------------ | ------------ | ------------ | ------------
http://donelistapi.com | `/company` e `/users` | `/1` | `?page=True`

**Existe outra forma de enviar dados para a API através do Body, o dado é enviado no corpo da requisição.**

Os tipos de parâmetros serão abordados posteriormente

### Vantagens da API REST

* Múltiplos Clientes - Vários FrontEnds podem usar o mesmo BackEnd, seja para Web, Mobile, API Pública, serviços externos etc.
* Utiliza o JSON para fazer a comunicação, ou seja, Vue, Angular, ReactJS, ReactNative podem usar o mesmo BackEnd, pois consegue trabalhar com formatos JSON.

### HTTP Codes

> Toda resposta que o BackEnd retorna, ela vem junto um HTTP Code, de 3 dígitos com o status da resposta. E eles tem o seu próprio padrão

Principais HTTP Codes:

HTTP Code | Descrição | Exempos
------------ | ------------ | ------------
1XX | HTTP Code informativo | n/a
2XX | HTTP Code de Success, toda resposta que começa com 2 significa que o procedimento foi finalizado corretamente | 200: Success, 201: Created
3XX | HTTP Code de redirecionamento | 301: Moved Permanently, 302: Moved
4XX | HTTP Code de erro do cliente (Client Error) | 400: Bad Request, 401: Unauthorized, 404: Not Found
5XX + | HTTP Code de erro no servidor (Server Error) | 500: Internal Server Error

---

## Criando um projeto NodeJS

* Primeiro cria-se um diretório para a aplicação Node.
* `yarn init -y` para iniciar um projeto e criar o arquivo `package.json`.
  * Package.json é o arquivo que armazena as informações de dependências do nosso projeto e algumas infos como _nome_, _descrição_, _versão_ etc.
* Normalmente, começamos com uma estrutura de pastas, normalmente usamos:

```yaml
- NodeJSProjectFolder
  - src
    - index.js
  - package.json
```

* Para desenvolver um BackEnd, o primeiro e um dos mais importantes pacotes que precisaremos instalar é o `express`, podemos instalar usando `yarn add express` no terminal (Disponibiliza a inclusão de rotas, urls diferentes com diferentes retornos e gerencia elas, assim como os middlewares).

Para criar uma aplicação funcional com NodeJS e Express, usamos:

```javascript
const express = require('express');
const app = express();
app.get('/', (request, response) => {
  return response.send('working')
});
app.listen(3333);
```

Executando `node src/index.js` e acessando o `http://localhost:3333/` no browser será possível observar o retorno passado.

---

### Nodemon

É muito chato toda vez que uma alteração for feita no código, precisar ir no terminal, cancelar o processo e executar novamente. Para solucionar esse problema, podemos instalar o _nodemon_, um pacote que reinicia o server automaticamente sempre que há mudanças.

Porém, quando a aplicação for ao ar, essa dependência não será necessária, para isso, adicionamos ela como **dependência de desenvolvimento usando a flag _-D_**: `yarn add nodemon -D`.

Agora, podemos digitar `yarn nodemon src/index.js` e fazer qualquer alteração nos arquivos que o servidor já irá reiniciar automaticamente.

Para otimizar ainda mais, podemos criar um script no `package.json` para não precisar digitar `yarn nodemon src/index.js` toda vez que for inicializar um servidor. Podemos então acrescentar no package.json:

```
"scripts": {
  "dev" : "nodemon src/index.js"
}
```

Para otimizar ainda mais, podemos mudar a propriedade `main` para `src/index.js`, assim, o trecho do script `dev` ficaria apenas `nodemon`:

```
[...]
"main": "src/index.js",
"license": "MIT",
"scripts": {
  "dev" : "nodemon"
}
[...]
```

Apenas o comando `nodemon` ele irá buscar o arquivo declarado na propriedade `main` do `package.json`. Podemos então inserir no terminal apenas o comando `yarn dev` e ele já irá iniciar o servidor com o arquivo `src/index.js`.

---

## Usando Métodos HTTP

Para usar outros métodos HTTP, podemos apenas alterar o _tipo_ do método:

```javascript
// Recurso: Users
app.get('/users', (request, response) => {
  return response.json([
    'usuario1',
    'usuario2'
  ]);
});
/*
* Roda para deletar
* Perceba que o recurso é o mesmo, a URL não muda, apenas o método.
*/
app.delete('/users', (request, response) => {
  return response.json({})
});
});
```

Assim como os métodos `GET` e `DELETE`, `PUT` e `POST` também são declarados dessa forma. Claro que acima é apenas um exemplo, em um sistema normal, estaríamos inserindo, lendo e removendo dados de um Banco de Dados.

## Tipos de Parâmetros

> Os tipos de parâmetros são formas do FrontEnd enviar algum tipo de informação para o backend.

Temos 3 principais tipos de parâmetros:

1. Query Params (Normalmente usados para filtro). São enviados na própria URL!
2. Route Params (Identifica recursos para atualizando ou deletando). São enviados na própria URL
3. Request Body (Conteúdo ao criar ou editar um recurso)

### Query Params

Usamos os query params inserindo uma interrogação (?) no final da Url, com a sintaxe `<chave>=<valor>` e inserimos mais query params com o _&_:

```
http://donelistapi.com/users?Male=true&State=Fortaleza
```


Para capturar todos os _Query Params_, na função do método HTTP usamos:

```javascript
const query  = request.query;
```

Ele irá retornar um objeto com todas as chaves e valores (No exemplo dado com a URL acima, o objeto _query_ será `{'Male': true, 'State': 'Fortaleza'}`).

### Route Params

Normalmente usamos os Route Params para indentificar recursos que estão sendo atualizados ou deletados. Os Route Params precisam ser definidos na Rota, portanto, definiremos um Route Param chamado de _ID_ na nossa rota GET:

```javascript
//Perceba os dois pontos antes do nome, é o caractere que declara o Route Param
app.get('/users/:id')
```

Agora vamos usar o método GET para capturar apenas um usuário de _id_ = 8:

```
http://donelistapi.com/users/8
```

Para capturar todos os _Route Params_, na função do método HTTP usamos:

```javascript
const params = request.params;
```

Ele irá retornar um objeto com todas as chaves e valores (No exemplo dado com a URL acima, o objeto _params_ será `{'id': 8}`).

Podemos confirmar o objeto recebido usando o `console.log`.

### Request Body

Suponha uma aplicação web com um _form_ para envio de dados (ex. criar um usuário). Então capturamos essa informação do Request Body em _JSON_.

Podemos utilizar o [insomnia](https://insomnia.rest/download/) para executar testes de requisições, pois podemos enviar protocolos de vários tipos, com Route Params, Query Params e Request Body!

Então escolhemos o corpo no formato _JSON_ e estruturamos a aplicação da seguinte forma:

index.js
```javascript
app.post('/users', (request, response) => {
    const body = request.body;
    console.log(body);
    return response.json({received: true});
});
```

Body do insomnia (Com método **POST**, e url **'http://localhost:3333/users'**)
```json
{
  "username": "brunosana",
  "email": "bruno@bruno.com"
}
```

Ao enviar a requisição percebemos no terminal a impressão _undefined_.

**Por padrão, o Express não interpreta os dados enviados em JSON. A API não irá entender. Para corrigir isso, logo após a linha `const app = express()` inserimos `app.use(express.json())`.**

---

## Aplicação Funcional

Para criar um protótipo de aplicação (protótipo pois é apenas para funcionar, em produção nunca será utilizado) no _index.js_ criamos um vetor `const projects = [];` antes das rotas, para usar como Banco de Dados.

Então, faremos a primeira rota GET para retornar todos os projetos:

```javascript
app.get('/projects', (request, response) => {
    return response.json(projects);
});
```

Para criar os projetos, faremos uma rota POST (Vamos assumir que um projeto possui um titulo 'title' e uma descrição 'desc'). Para registrar um _Unique ID_, poderíamos fazer com um inteior, mas vamos já usar um método usado em produção, que é o método `uuid` da lib `uuidv4`. Então, antes, precisamos instalar com `yarn add uuidv4` e logo depois importar no código com `const { uuid } = require('uuidv4');` (Perceba que já estamos usando a desconstrução). Então faremos:

```javascript
app.post('/projects', (request, response) => {
    const { title, desc } = request.body;
    const project = {id: uuid(), title, desc}
    projects.push(project);
    return response.json(project);
});
```

Precisamos então de uma rota para atualizar um único projeto, passando o seu ID como parâmetro:

```javascript
app.put('/projects/:id', (request, response) => {
    const { id } = request.params;
    const { title, desc } = request.body;
    const projectID = projects.findIndex(project => project.id === id);
    console.log(projectID);
    if(projectID < 0){
        return response.status(400).json({error: 'Not Found'});
    }
    const project = {id, title, desc};
    projects[projectID] = project;
    return response.json(project);
});
```

Para deletar um projeto, usamos a rota com o método _DELETE_:

```javascript
app.delete('/projects/:id', (request, response) => {
    const { id } = request.params;
    const projectID = projects.findIndex(project => project.id === id);
    if (projectID < 0){
        return response.status(400).json({error: 'Not Found'})
    }
    projects.splice(projectID, 1);
    return response.status(204).send();
});
```

Para buscar um único projeto, podemos fazer:

```javascript
app.get('/projects/:id', (request, response) => {
    const { id } = request.params;
    const projectID = projects.findIndex(project => project.id === id);
    console.log(projectID);
    if(projectID < 0){
        return response.status(400).json({error: 'Not Found'});
    }
    return response.json(projects[projectID]);
});
```

Ainda há como fazer a alteração da função que retorna todos os projetos para usarmos _FILTROS_ do **query params**. Suponha que vamos querer filtrar os projetos pelo título (parametro title):

```javascript
app.get('/projects', (request, response) => {
    const { title } = request.query;
    const results = title
    ? projects.filter(project => project.title.includes(title))
    : projects
    return response.json(results);
});
```