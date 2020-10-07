# Atalhos para trabalhar com CSS em ReactJS com Styled Components

## Encadeamentos

Desde a existência dos pré processadores de CSS (SASS, LESS etc) eles adicionam funcionalidades no CSS que são bem interessantes. Algo como **encadeamento**:

```
export const Form = styled.form`
    color: #ddd;
    
    input{
        flex: 1;
        height: 70px;
    }
`;
```

Declarando uma estilização de tag dentro do _Form_ (por exemplo) significa que todos os campos `input` dentro dessa tag `form` irão receber a estilização! (No CSS nativo seria `form input {...}`).

## &:hover

Se você quiser fazer uma animação, por exemplo, ao passar o mouse em cima de um botão, podemos usar a propriedade `&:hover`:

```
button{
    width: 210px;
    height: 60px;
    background: #04D361;
    border: 0;
    border-radius: 0 5px 5px 0;
    color: #FFF;
    font-weight: bold;
    &:hover{
        background: #fff;
    }
}
```

Usando o `&` estamos referenciando o próprio elemento, no caso, o `button`.

## Polished

Polished é uma biblioteca que foi criada pelos criadores do `styled-components` que permite trabalhar com cores, clarear, escurecer, aplicar transparência etc..

Primeiro precisamos instalar a biblioteca com `yarn add polished`

Ex. Usando o exemplo anterior, vamos criar um `button` que ao passar o mouse por cima, ele escureça em `20%`:

```
import styled from 'styled-components';
import { shade } from 'polished';

export const Botao = styled.button`
    width: 210px;
    height: 60px;
    background: #04D361;
    border: 0;
    border-radius: 0 5px 5px 0;
    color: #FFF;
    font-weight: bold;
    transition: background-color 0.15s;
    &:hover{
        background: ${shade(0.2, '#04D361')};
    }
`;
```

Usamos a função `shade`, passando como primeiro parâmetro o valor da transparência (20%) e o segundo, a cor como referência.

## Bordas 100% redondas

Para deixar uma imagem 100% redonda, o único pré requisito é que precisa ser uma imagem quadrada (obviamente).
Feito isso, basta inserir um `border-radius: 50%;` e é sucesso.

## Utilizando ícones

Para utilizar ícones em ReactJS, dentre as várias formas, você pode instalar uma lib  _react-icons_ com `yarn add react-icons`. Vem vários pacotes. [Referência aqui!](https://react-icons.github.io/react-icons/).

Inserimos o ícone importado como uma tag. Ex:

```javascript
import React from 'react';
import { FiChevronRight } from 'react-icons/fi';

const Dashboard: React.FC = () => {
    return <FiChevronRight size={20} />;
}

export default Dashboard;
```

## Verificar tag anterior

Normalmente, quando temos uma lista com vários itens, com tags que se repetem, praticamente sempre temos que colocar um espaçamento entre eles para garantir que eles não encostem um nos outros.

Porém, o primeiro elemento, normalmente, não faz muito sentido aplicar esse espaçamento, pois ele está no topo, nenhum outro elemento repetido estará acima dele.

Para isso, existe uma verificação que pode ser feita, para que o espaçamento (ou estilização qualquer) seja aplicado apenas a partir do segundo elemento.

Possui a seguinte sintaxe (usamos como exemplo a tag A):

```CSS
a + a {}
```

Essa verificação será feita da seguinte forma

* Elemento à esquerda -> É o tipo da tag que vai ser checada se existe antes do elemento
* + -> Precedido - significa que a operação a ser feita será de acordo com o elemento anterior
* Elemento à direita -> Tag que será aplicada a estilização caso feita a comparação. Ela é comparada com o elemento à direita.

Em ReactJS, caso essa operaçã seja aplicada de forma interna e o primeiro elemento (à direita) é igual ao elemento pai, usamos o `&`:

```CSS
& + a {}
```

## Explicitar estilização em apenas uma tag do escopo

Suponha a seguinte situação:

```JSX
<Containter>
    <form>
        <input placeholder="E-mail" />
        <button>Search</button>
        <a>Forgot email</a>
    </form>
    <a>Help</a>
</Container>
```

E em seu arquivo `styles.ts`:

```JSX
export const Container = styled.div`
    form{
        input{
            width: 100%;
            padding: 16px;
        }
        a{
            width: 100%;
            color: #dfdfdf;
        }
    }
`;
```

Porém, você quer estilizar a tag `a` **abaixo do form individualmente sem comprometer a outra tag `a` que está dentro do form.**

Caso você usasse normalmente a estilização, as estilizações das tags seriam comprometidas. Para isso, **vamos usar um artifício informando que as tags a serem estilizadas serão SOMENTE as que estão no mesmo escopo da declaração. Ou seja, se a tag estiver dentro ou fora de outra tag, esta será ignorada**.

Sintaxe: `> a {}`, portanto:

```JSX
export const Container = styled.div`
    form{
        input{
            width: 100%;
            padding: 16px;
        }
        a{
            width: 100%;
            color: #dfdfdf;
        }
    }
    > a {
        color: #ababab;
        
        &:hover{
            colo: #fff;
        }
    }
`;
```
