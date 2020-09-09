## ReactJS Notes

### Tópicos:

* React
* Diferenças entre React, ReactJS e React Native
* Vantagens do React
* Babel/Webpack | OBS

## React

> React é uma biblioteca para construção de interfaces (Tudo o que o usuário vê, seja html, css e javascript). React serve para Web, Mobile, Realidade Virtual etc.

PS:

* **É utilizado para construção de SPA** (Single-Page Applications). Antigamente, quando não tinhamos o conceito de SPA,  toda rota do Back-end era acessada um html era retornado. O que mudou com o SPA é que o Back-end retorna apenas o JSON , o Front-end controla as rotas e a parte de consumir os dados, assim como exibir aos usuários. Todo o roteamento da aplicação é com o Front-end. **O conceito de SPA é que tudo é uma página só, ela nunca recarrega**, dando mais performance para o usuário final.
* **React é um framework**, pois é uma lib. Ainda que o ecossistema React (ReactJS, React Native, Redux etc.), podemos chamar de framework, pois é um conjunto de ferramentas que auxiliam o desenvolvimento Front-end.
* **Tudo fica dentro do Javascript**. Css, Imagens, Html e toda a estrutura fica dentro do javascript da aplicação. Isso se dá pois dentro do React temos uma funcionalidade chamada _JSX_, que é a junção do Javascript com o xml, permitindo escrever elementos html. E com o React, podemos criar nossos próprios elementos.

## Diferenças entre React, ReactJS e React Native

1. **React**

> Se refere à biblioteca de construção de interfaces e componentização. Usada tanto no React Native quanto no ReactJS.

2. **ReactJS**

> Se refere ao comportamento do React diretamente no Browser. Quanto a junção do React com o React Dom (lib do facebook), que controla a árvore de elementos do Browser, chamamos de ReactJS.

3. **React Native**

> Se refere à soma do React com a lib que lida com elementos nativos. Chamamos então de React Native.

## Vantagens do React

1. **Organização do código**. Se deve a um conceito vindo com o react chamado _Componentização_ (conceito que Angular, Vue mais tarde seguiram o mesmo conceito). Componentização nada mais é do que dividir partes do código em componentes e cada componente tem funcionalidades específicas. **A divisão de componentes acontece quando a divisão de a lógica de um componente é feita sem interferir no funcionamento do restante da aplicação. Um componente pode e geralmente contém código html, css etc.**
2. **Divisão de responsabilidades**. Com o Back-end e Front-end independentes, existe a vantagem de fazer com que o Back-end se responsabilize apenas pela regra de negócio (Cálculos, integração com meios de pagamentos, acesso ao DB etc) e o Front-end fica com a interface.
  * Com esse padrã podemos fazer uma API para múltiplos clientes, ou seja, para sistemas Web, Mobile, interfaces diferentes consumindo a mesma API.
3. Programação declarativa. Ao invés de escrever os passos para o browser montar o componente, é informada as condições para que essa montagem seja feita.

Ex. de código usando JSX para criar um elemento _button_ em ReactJS retornando diretamente o html:

```jsx
function Botao(){
    return(
        <button type="button">
            Click-me
        </button>
    );
}
```

E podemos importar e usar esse elemento em outro local:

```jsx
function Header(){
    return <Botao />
}
```


Esses elementos são componentes criados no React.

---

## Babel/Webpack | OBS


* **O browser não entende o código. Os browsers não são preparados para trabalhar com as linguagens juntas.**
* **O Babel é responsável por converter o todo código em um formato JS, HTML e CSS legível a todos os browsers para garantir compatibilidade.**
* **Webpack:**
  * **Criação do Bundle (Arquivo que contém todo o código JS da aplicação). Embora possa ter vários arquivos em desenvolvimento o webpack transforma todo o código compilado pelo Babel em um único arquivo.**
  * **Ensinar ao JavaScript como importar CSS, Imagens etc. Por padrão, o Babel não entende importação de arquivos CSS, Imagens etc. Ele faz apenas a parte de JS. O Webpack possui a funcionalidade _loaders_ que mostra ao JS como importar arquivos diferentes de arquivos JS.**
  * **Live Reload com Webpack Dev Server. Toda vez que uma alteração for feita, o servidor reinicia automaticamente (semelhante à funcionalidade do Nodemon).**