## Rodar uma aplicação local direto no docker

1. Você precisa criar na raiz do projeto um arquivo `Dockerfile` (Arquivo com configurações descritivas)


````
FROM <nome_da_imagem>

WORKDIR <diretorio_dos_arquivos // # Quando irá ser rodado a criação do container, definimos um diretório onde as informações estarão contidas.> 

COPY package.json ./ #copiamos o arquivo que irá instalar as dependências da aplicação. (COPY <file> <directory>)

RUN npm install #Roda o comando para baixar as dependências

COPY . . # Copia toda a estrutura gerada para a pasta raiz

CMD ["npm", "run", "dev:server"] # Executa o comando 'npm run dev:server' para iniciar o script
````

Precisamos agora incluir um arquivo `.dockerignore` para passar os diretórios que precisam ser ignorados pelo docker:

````
node_modules
.git
.vscode

````

para esse exemplo, utilizamos a seguinte estrutura:

````
FROM node

WORKDIR /usr/app

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 3333

CMD ["npm", "run", "dev:server"]
````

Finalizado o arquivo podemos ir no CMD (na pasta da aplicação) e executar a criação da imagem:

```
docker build -t <nome_da_imagem> <onde_esta_o_dockerfile>
```

Para esse exemplo, usamos
```
docker build -t projects .
```

Após instalar a imagem, podemos dar um `docker run -p 3333:3333 <nome_da_imagem>`

OBS: `-p 3333:3333` faz um mapeamento de portas, onde no localhost quando for chamado a porta 3333, o docker irá buscar a porta 3333.

Podemos ainda acessar a estrutura de arquivos do container (precisamos do nome do container com `docker ps` então podemos ver o nome) com `docker exec -it <container_name> /bin/bash` (ou caso esteja no windows, altere o diretório para deixar com duas barras: `//bin//bash`).


### Melhorando a execução

Quando utilizamos o dockerfile temos certas limitações, como por exemplo ao atualizar o código do repositório local, não atualiza no container.

Para isso, vamos utilizar o docker-compose:

> Gerenciador de containers, possibilitando configurar os serviços que precisamos rodar, setar dependências entre serviços e deixar a aplicação mais automatizada.

Para isso, precisamos criar, primeiro, um arquivo na raíz do projeto chamado `docker-compose.yml`.

```
version: <versao_do_docker-compose>

services: # Serviços que o docker precisará rodar.
    app: #nome do serviço
        build: #comando que irá rodar
        ports: #portas utilizadas
            - 3333:3333
        volumes: # diretorio da aplicação
            - .:/usr/app # passa o redirecionamento do diretório atual para o da aplicação (que definimos no dockerfile)
```

No exemplo, ficou:

```
version: "3.7"

services: 
    app:
        build: .
        container_name: <nome_do_container>
        ports:
            - 3333:3333
        volumes: 
            - .:/usr/app
```

Para executar o docker, usamos `docker-compose up`

Ele irá gerar o novo container.

Para fazer com que o docker execute o UP em background (para não ficar In real time no terminal) passamos a flag `-d`, portanto, ficará `docker-compose up -d`.

Caso queira novamente observar no terminal a execução do docker, use `docker logs <nome_do_container> -f`.


### Comandos úteis

1. docker ps

Mostra todos os containers ativos

PS: para mostrar todos os containers, use a flag -a

2. docker rm <container_id ou container_name>

Remove o container

3. docker stop <container_id ou container_name>

Para a execução de um container ativo

4. docker start <container_id ou container_name>

Inicia a execução de um container parado

5. docker-compose up -d

Inicia o container em background

6. docker-compose stop

Para a execução de um container parado

7. docker-compose down

Remove um container

8. docker-compose start

Inicia um container com o docker-compose

9. docker exec -it <container_id ou container_name> <directory>

Entra em um container do docker em um diretório específico (para sair do container, `exit` no terminal do container)

10. docker logs <container_id ou container_name>>

Observa os últimos logs de um container

11. docker logs <container_id ou container_name> -f

Observa os últimos logs de um container e fica observando os próximos