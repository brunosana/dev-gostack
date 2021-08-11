## Criar container secundário

Para esse exemplo vamos utilizar o container do Postgresql com Typeorm

Em `docker-compose.yml` temos a seguinte estrutura:

```yml
version: "3.7"

services:
    database_post:
        image: postgres
        container_name: post_database
        restart: always
        ports:
            - 5432:5432
        environment: 
            - POSTGRES_USER=postUser
            - POSTGRES_PASSWORD=postPass
            - POSTGRES_DB=post
        volumes:
            - pgdata:/data/postgres
    app:
        build: .
        container_name: post
        ports:
            - 3333:3333
        volumes:
            - .\:/usr/app

volumes:
    pgdata:
        driver: local
```

E nosso arquivo `database/index.ts`, temos:

```typescript
import { createConnection } from 'typeorm';

createConnection();
```

executando o `docker-compose up`, os containers são criados normalmente, porém, o container do app não irá se comunicar com o container do banco, por uma questão de direcionamento de ips. Para isso, vamos então procurar qual o IP do container do banco.

Para isso, usamos:

```
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container_name>
```

Vamos conseguir capturar o IP do container.

Uma outra forma mais curta de capturar o IP, é utilizando `docker exec <container_name> cat /etc/hosts`.

Nós podemos verificar que ambos os containers possuem IPs diferentes. Para corrigir isso, podemos definir que os dois containers trabalharão na mesma rede. Para isso, após a propriedade `volumes` de cada container, usamos:

```yml
network_mode: host
```

Podemos derrubar o container agora com `docker-compose down` e logo depois `docker-compose up -d`


# CASO DE ERRO

Pode ser que caso a versão do docker seja superior, a propriedade `network-mode` não irá funcionar.

Para isso, no container da aplicação, inserimos o trecho:

```yml
links:
	- database
depends_on: 
	- database
```

E no arquivo `database/index.ts`, colocamos:

```typescript
import { createConnection, getConnectionOptions } from 'typeorm';

interface IOptions {
    host: string;
}

getConnectionOptions().then(options => {
    const newOptions = options as IOptions;
    newOptions.host = '<NOME_DO_SERVICE_DO_BANCO_AQUI>';
    createConnection({
        ...options,
    });
});
```
