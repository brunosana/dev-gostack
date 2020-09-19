# Configurando o TypeORM

### Tópicos

* Configurando o TypeORM
* Criando tabela de agendamento
* Criando model de agendamento

## Configurando o TypeORM

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

---

## Criando tabela de agendamento

Precisamos inserir o trecho no `ormconfig.json`:

```JSON
"migrations": [
    "./src/database/migrations/*.ts"
],
"cli": {
    "migrationsDir": "./src/database/migrations"
}
```

O TypeORM oferece suporte tanto para JS como TS. Portanto, por padrão, quando rodamos a criação da migration ele irá gerar elas em .JS.

Para isso, em `package.json` inserimos em scripts:

```JSON
"typeorm": "ts-node-dev ./node_modules/typeorm/cli.js"
```

Podemos rodar agora o comando `yarn typeorm` e verificar que ele já reconhece na CLI. Agora, podemos criar nossa primeira migration com `yarn typeorm migration:create -n CreateAppointments`. PS: CreateAppointments é o nome da tabela que será criada no banco de dados. Será gerado um arquivo em `src/database/migrations`.

> **Migrations** controlam versões e alterações simultâneas no Banco de Dados. Ao invés do programador alterar diretamente na base de dados, ele cria um arquivo migration com as instruções das alterações no Banco de Dados. Outros usuários precisam apenas executar as migrations para equalizar a estrutura dos Bancos de Dados.

Ou seja, no método `public async up` colocamos o que irá acontecer no banco de dados quando a migration for executada!

O Método `public async down` será um fallback, terá toda a informação inversa que o método `up`. Ele desfaz o que foi feito no método `up` (Ex. Se no método `up` uma tabela for criada, no `down` essa mesma tabela é excluída).

Alguns erros serão exibidos nessa migration por conta do eslint, para isso, inserimos uma `rule` em `.eslintrc.json`:

```JSON
"class-methods-use-this": "off"
```

Em `up`, escrevemos:

```typescript
public async up(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.createTable(
        new Table({
            name: 'appointments',
            columns: [
                {
                    name: 'id',
                    type: 'varchar',
                    isPrimary: true,
                    generationStrategy: 'uuid',
                },
                {
                    name: 'provider',
                    type: 'varchar',
                    isNullable: false,
                },
                {
                    name: 'date',
                    type: 'timestamp with time zone',
                    isNullable: false,
                },
            ],
        }),
    );
}

public async down(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.dropTable('appointments');
}
```

Estamos criando uma tabela com 3 colunas, `id`, `provider` e `date`. No método `down` excluímos a tabela.

PS: Na coluna `id` o campo generationStrategy é uma forma de automatizar a criação dos IDs ao serem inseridos no banco de dados.

Para executar as migrations, usamos `yarn typeorm migration:run`. No DBeaver já é possível observar as tabelas em `gostack_gobarber/Schemas/public/Tables`.

**SÓ PODEMOS ALTERAR UMA MIGRATION SE A MESMA NÃO ESTÁ NO SISTEMA DE CONTROLE DE VERSÃO (Ex. GIT). SE NÃO, OBRIGATORIAMENTE, CRIAMOS UMA NOVA MIGRATION**

Caso cumprido o único requisito, podemos desfazer as migratons com `yarn typeorm migration:revert` e as tabelas serão excluídas, podemos alterar a migration e novamente executar `yarn typeorm migration:run`.

Para ver as migrations que já foram executadas, podemos usar o `yarn typeorm migration:show`.

## Model de Agendamento

Precisamos relacionar o model `Appointment.ts` com o banco de dados.

Antes, no arquivo `tsconfig.json` precisamos habilitar a funcionalidade do Typescript chamada **decorators**.
Com a notação `@`. Para isso, descomentamos as seguintes linhas:

```JSON
"experimentalDecorators": true,
"emitDecoratorMetadata": true,
```


No model, importamos o `Entity`, uma entidade que irá ser salva no banco de dados:

```typescript
import { Entity, Column, PrimaryGeneratedColumn } from 'typeorm';

@Entity('appointments')
class Appointment {
    @PrimaryGeneratedColumn('uuid')
    id: string;

    @Column()
    provider: string;

    @Column('timestamp with time zone')
    date: Date;
}

export default Appointment;
```

Decorator funciona como uma função, no caso do `@Entity('appointments')` **e enviará a classe como parâmetro** da função. O parâmetro dentro dos parênteses é o nome da tabela onde uma instância do model irá ser salvo.

Os outros Decorators servem para informar o que são colunas do banco de dados e o que são apenas parâmetros da aplicação!

O decorator `PrimaryGeneratedColumn` usamos para a chave primária, que, por definição, na migration, é gerada automaticamente, passando o 'uui' como parâmetro.

O decorator `Column` sem parâmetros trata a variável como varchar, e o Date, informamos o mesmo tipo de dado, com 'timestamp with time zone'.

PS: Com o decorator `Entity` inserido acima da classe, o construtor é criado de forma automática. Então podemos excluir o construtor e usar métodos específicos do TypeORM para criar instâncias do Model.

Porém como agora estamos utilizando o construtor do TypeORM, ao não inicializar variáveis dos Models, o Typescript irá retornar um erro no model. Para isso, vamos no arquivo `tsconfig.json` e descomentamos a seguinte linha:

```JSON
"strictPropertyInitialization": false
```

Agora não temos mais erros nas classes dos models. O Model já está pronto e precisamos apenas alterar o repositório para inserir de forma correta!