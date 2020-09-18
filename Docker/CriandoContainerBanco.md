# Criando um Container do Banco com o Docker

## Instalando o Postgress

O repositório da Imagem do postgress se encontra [nesse link](https://hub.docker.com/_/postgres), onde é possível encontrar também todas as versões disponíveis para download. Também tem um tutorial ensinando como instalar.

Para começar, inserimos no terminal:

`docker run --name NOME_DA_IMAGEM -e POSTGRES_PASSWORD=senhaDoDocker -p <porta_do_sistema>:<porta_docker> -d nome_da_imagem`

Inserimos então:

`docker run --name gostack_postgres -e POSTGRES_PASSWORD=docker -p 5432:5432 -d postgres`

Usando `docker ps` podemos ver a imagem já rodando!

Caso a imagem não seja visível com o comando acima, usamos `docker ps -a` que será listado todos os containers que está na máquina. Procuramos a imagem que criamos, no caso a gostack_postgres, e podemos observar o seu status, caso seja _exited_ ou algo próximo, podemos ver oq aconteceu, pegando o número *CONTAINER_ID* e inserindo `docker logs CONTAINER_ID`.

Para parar a execução de um container, usamos `docker stop CONTAINER_ID`

OBS: Normalmente quando reiniciamos o SO ou fechamos o Docker, o container também é parado. Portanto, caso precise novamente executar um container (verificamos se este já não está sendo executado) inserimos `docker start CONTAINER_ID` ou `docker start IMAGE_NAME`.

Para acessarmos o banco de dados dentro do docker, utilizaremos o [DBeaver](https://dbeaver.io/). Como alternativa, também temos o [postbird](https://www.electronjs.org/apps/postbird), caso não consiga instalar o DBeaver.

Ao Abrir pela primeira vez, vamos na aba (menu esquerdo) _ALL_ e clicamos em _PostgreSQL_. Ao clicar, alguns downloads podem ser requisitados, é só clicar em _Download_:

```
host: localhost
port: 5432 -> A mesma que usamos ao criar a imagem!
database: postgress -> criada por padrão
username: postgress
password: docker
```

Na aba _PostgreSQL_ ativamos o checkbox _Show All Databases_.

Clicamos em _finish_ e já podemos ver a conexão no painel esquerdo.