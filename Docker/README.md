## Docker Notes

### Tópicos:

* Estratégias de Abstração
* Conceitos Docker

## Estratégias de Abstração

> Existem 3 principais estratégias para conseguir efetuar o CRUD (Create - Read - Update - Delete) em um banco de dados.

1. Lidando direto com o Driver Nativo do banco de dados.

Ex. Suponha que você tenha uma aplicação NodeJS e precise integrar com o Banco de Dados Postgres.

Para lidar com o Driver Nativo, nesse caso, utilizaríamos o [node-postgres](https://node-postgres.com/), onde podemos fazer querys utilizando o médoto `query` e escrevemos a nossa _query_:

```javascript
client.query('SELECT NOW() as now', (err, res) => {
  if (err) {
    console.log(err.stack)
  } else {
    console.log(res.rows[0])
  }
})
```

2. Query Builder

Ao invés de construir as querys utilizando a sintaxe do banco, como a primeira forma, escrevemos em Javascript.

Para o mesmo exemplo dado antes, usaríamos o [knexjs](http://knexjs.org/) e escreveríamos da seguinte forma:

```javascript
knex.select('title', 'author', 'year').from('books');
```

O Output será convertido para uma query SQL, dependendo do database.

3. ORM - Object Relational Mapping

É o maior nível de abstração possível. Quase não usando querys.

São mapeados registros da tabela do Banco de Dados com Objetos JS. Ou seja, criamos models do Objeto em JS/TS e cada instância de model (CRUD) reflete no Banco de Dados.

Em NodeJS temos algumas boas opções para trabalhar com ORM. O [sequelize](http://sequelize.org/) para Javascript, e pro Typescript o [TypeORM](https://typeorm.io/#/), embora também possa ser usado com JS, porém é muito mais eficaz com o Typescript.

É usado da seguinte forma:

Nos models, usamos uma sintaxe do Typescript chamada _Decorator_ informando quais são as colunas. Quando criamos uma instância do Model, ao ser salvo (usando o conceito de repository) ele é armazenado no Banco de Dados, onde podemos capturar esses dados com o método _find_ ou apenas um elemento com o *find_one*.

A sintaxe é diferente, é um ORM puro, não usamos as querys para Banco de Dados (como `select * from table`), usamos apenas Typescript.

Por baixo dos panos, o TypeORM usa o Knex, ou seja, caso haja a necessidade de utilizar uma query complexa, podemos usar o Knex com o TypeORM.

A vantagem ao usar maior abstração para fazer inserção e consultas em banco de dados é que independente do banco utilizado (Postgres, SQLite, Oracle, Microsoft SQL Server, MySQL) a sintaxe é a mesma. Ou seja, caso haja uma mudança de Bando de Dados, não alteramos o código.

## Conceitos Docker

> Controla os serviços da nossa aplicação (Enviar Email, Armazenar dados etc.).

**Como funciona**:

* Criação de Ambientes Isolados (Ambientes que não interferem em outras techs/ferramentas no servidor). Ou seja, para cada serviço for instalado, ele ficará em um subsistema (chamado de container), sem comprometer outros serviços ou o SO.

Containers normalmente expõem portas para comunicação. Ex. O MySQL tem como padrão 3306, Postgress 5432, MongoDB 27017. Usamos o mesmo localhost para conexão, mudando apenas a porta.

---

**Principais Conceitos**:

* **Imagem** - Serviço disponível no Docker. Postgress, MySQL e outras são imagens, tecnologias que podemos colocar em containers na aplicação
* **Container** - Instância de uma imagem, ou seja, se estamos usando Postgres e Mongo, estamos utilizando um Container para cada.
* **Docker Registry** - Todas as imagens do Docker estão na Docker Registry (Docker Hub), podemos cadastrar nossas imagens lá também.
* **Dockerfile** - Receita de montagem de uma imagem: Define como a imagem da nossa aplicação funciona em um ambiente totalmente do zero