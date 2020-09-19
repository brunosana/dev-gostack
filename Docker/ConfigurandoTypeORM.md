# Configurando o TypeORM

### Tópicos

* Configurando o TypeORM
* Criando tabela de agendamento
* Criando model de agendamento
* Repositório do TypeORM

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
"entities": [
    "./src/models/*.ts"
],
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
                    default: 'uuid_generate_v4()',
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
PS: Na coluna `id` o campo default recebe uma função que dentro do postgres ele gera o Universal Unique ID

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

## Repositório do TypeORM

Por padrão, o TypeORM tem um repositório padrão com operações padrão que são feitas em um Banco de Dados (CRUD). Portanto, dentro do AppointmentsRepository.ts teremos:

```typescript
import { EntityRepository, Repository } from 'typeorm';
import Appointment from '../models/Appointment';

@EntityRepository(Appointment)
class AppointmentsRepository extends Repository<Appointment> {
    public async findByDate(date: Date): Promise<Appointment | null> {
        const findAppointment = await this.findOne({
            where: { date },
        });
        return findAppointment || null;
    }
}

export default AppointmentsRepository;
```

* Importamos do TypeORM o `EntityRepository`e o `Repository`
* Podemos retirar o array e o construtor, assim como o método `all`. Pois não iremos mais instanciar um vetor e o próprio repositório do TypeORM tem um método equivalente ao `all`.
* Também podemos remover o método `Create`.
* O único que irá sobrar será o `findByDate`, que é uma regra de negócio e será alterado posteriormente.
* Removeremos também a interface `CreateAppointmentDTO`
* Inserimos um decorator `EntityRepository` acima da classe, passando o Model como parâmetro.
* Criamos uma herança com o `extends Repository`, passando o model como parâmetro, que é onde irá ter todos os métodos de conversa com o Banco de Dados.
* Alteramos a função `findByDate`, usando o `this.findOne` passando no parametro `where` o `date`. Como será uma busca no banco de dados, **a função se tornou assíncrona e o seu retorno uma Promisse**, podendo retornar um Appointment ou Null.

Podemos modificar o `CreateAppointmentServices` agora:

```typescript
import { startOfHour } from 'date-fns';
import { getCustomRepository } from 'typeorm';
import Appointment from '../models/Appointment';
import AppointmentsRepository from '../repositories/AppointmentsRepository';

interface Request {
    provider: string;
    date: Date;
}

class CreateAppointmentService {
    public async execute({ provider, date }: Request): Promise<Appointment> {
        const appointmentsRepository = getCustomRepository(
            AppointmentsRepository,
        );

        const appointmentDate = startOfHour(date);

        const findAppointmentInSameDate = await appointmentsRepository.findByDate(
            appointmentDate,
        );

        if (findAppointmentInSameDate) {
            throw Error('This appointment is alredy booked');
        }

        const appointment = appointmentsRepository.create({
            provider,
            date: appointmentDate,
        });

        await appointmentsRepository.save(appointment);

        return appointment;
    }
}

export default CreateAppointmentService;
```

* Removemos a parte do construtor e declaração da variável e deixamos apenas a função `execute`
* Importamos o método `getCustomRepository` do `typeorm`
* Na função execute atribuímos à variável `appointmentsRepository` o método `getCustomRepository` passando como parâmetro o `AppointmentsRepository` importado no Service.
* Podemos agora tirar o `this` de todas as declarações da variável `appointmentsRepository` pois a declaração está no escopo da função.
* A variável `findAppointmentInSameDate` recebe uma promise, então inserimos o `await`
* Há só uma pequena diferença no `create` do TypeORM. **Ele apenas cria a instância do objeto Appointment mas não salva no Banco de Dados**.
* Para salvar usamos o método `save` logo após criar a instância
* Como o registro será inserido no banco, usamos o `await`, transformando a função em assíncrona e o seu retorno uma Promise

Com o service salvo, **precisamos agora alterar a Rota**, que será modificada para o Repositório do TypeORM.

O arquivo `appointments.routes.ts` ficará:

```typescript
import { Router } from 'express';
import { parseISO } from 'date-fns';
import { getCustomRepository } from 'typeorm';
import AppointmentsRepository from '../repositories/AppointmentsRepository';
import CreateAppointmentService from '../services/CreateAppointmentService';

const appointmentsRouter = Router();

appointmentsRouter.post('/', async (request, response) => {
    try {
        const { provider, date } = request.body;

        const parsedDate = parseISO(date);

        const createAppointment = new CreateAppointmentService();

        const appointment = await createAppointment.execute({
            provider,
            date: parsedDate,
        });

        return response.json(appointment);
    } catch (err) {
        return response.status(400).json({ error: err.message });
    }
});

appointmentsRouter.get('/', async (request, response) => {
    const appointmentsRepository = getCustomRepository(AppointmentsRepository);
    const appointments = await appointmentsRepository.find();
    return response.json(appointments);
});

export default appointmentsRouter;
```

* Removemos a variável `appointmentsRepository` do começo do arquivo para ser instanciado dentro das rotas.
* Na rota do tipo GET, a varável `appointmentsRepository` agora recebe o método `getCustomRepository` recebendo como parâmetro o `AppointmentsRepository`, igual ao Service.
* O método `all` foi substituído pelo método `find`
* Como o método `find` é uma promise, inserimos antes o `await` e deixamos a função como Assíncrona
* Na rota do tipo POST, precisamos remover os parâmetros do construtor do `CreateAppointmentService`, já que ele já tem os dados do Repository do TypeORM.
* A função `execute` recebe o await por ser uma promise
* A função que trata a rota agora é assíncrona

O servidor ainda não está funcionando, pois quando o Typescript usa a sintaxe de decorators precisamos instalar uma dependência com `yarn add reflect-metadata`.

Essa dependência precisa ser importada no primeiro arquivo da aplicação, no caso, o `server.ts`, inserimos:

```javascript
import 'reflect-metadata';
```

Agora podemos rodar o servidor com `yarn dev:server` e testar as rotas.