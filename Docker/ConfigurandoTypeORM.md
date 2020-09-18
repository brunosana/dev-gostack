# Configurando o TypeORM

Precisamos instalar duas libs antes, de auto explicação, com `yarn add typeorm pg`

O que temos que fazer é criar um arquivo de configuração `ormconfig.json`.

Dentro desse arquivo começamos com a configuração da base de dados (ref [aqui](https://typeorm.io/#/connection-options)):

```JSON
{
    "type": "postgres",
    "host": "localhost",
    "port": 5432,
    "username": "postgres",
    "password": "docker",
    "database": "gostack_gobarber"
}
```

Podemos criar um arquivo em `src/database/index.ts`:

```javascript
import { createConnection } from 'typeorm';

createConnection();
```

Essa função procura o arquivo `ormconfig.json` na estrutura do projeto, importar, ler os dados e fazer a conexão de forma automática. Por isso não passamos nenhum parâmetro. Poderíamos passar as informações no parâmetro da função, mas com o arquivo ormconfig podemos usar toda a CLI do typeorm que lê as credenciais do `ormconfig`.

Inserimos em `server.ts`:

```javascript
import './database';
```

E, ao rodar o `yarn dev:server`, ele pode retornar um erro caso a _database_ não exista, para isso, vamos no Dbeaver , no painel esquerdo, clicando em cima da conexão com o botão direito e logo em seguida em create->database.

Setamos o nome como `gostack_gobarber` e confirmamos a criação, agora podemos verificar que a conexão será bem sucedida!