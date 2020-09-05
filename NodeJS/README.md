## NodeJS Notes

## NodeJS

> Node é uma ferramenta que permite o uso do Javascript no BackEnd, que é onde ficam as regras de negócio, serviços externos (como API de pagamentos, Nota Fiscal etc), conexão com o Banco de Dados etc.

PS:

* **NodeJS não lida com eventos do usuário final** | O NodeJS não faz a interação por exemplo com o clique de botões, animação quando o mouse passa sobre algum elemento (que é como o javascript trabalha no FrontEnd). Os eventos são tratados pelas **Rotas de aplicação**.
* **NodeJS NÃO é uma linguagem, é uma Plataforma de desenvolvimento BackEnd**.
* **NodeJS é construido em cima da _V8_** | V8 é a máquina que roda por trás do Chrome (ele precisa de uma engine/motor para interpretar o Javascript e produzir uma resposta).
* NodeJS é comparável com -> PHP / Ruby / Python etc.

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
