# Isolar componentes em ReactJS

Suponha a seguinte situação, temos uma página de cadastro com o seguinte trecho:

```JSX
<input type="text" placeholder="E-mail" />
```

Esse `input` tem sua própria estilização, tamanho, fonte, cor e transição. Porém, temos outras páginas que utilizamos o Input com a mesma estilização da tag da página de cadastro.

Recriar esse componente em outra página seria completamente inviável pois o mesmo código seria reescrito e caso haja alguma alteração, teria que ser feito em todas as ocorrências onde o código é replicado.

Para isso, vamos criar um componente Input em `src/components/Input/index.tsx` e nessa mesma pasta, vamos inserir o nosso estilo com `styles.ts`.

Existem algumas considerações que precisamos ter ao isolar um componente:

1. Nada garante que as instâncias dele terão os mesmos valores.
2. Pode ser que para a aplicação seja necessário usar propriedades personalizadas
3. Para esse exemplo, podemos também usar à esquerda do input um `Icon`.

Dito isso, nosso arquivo ficará:

```JSX
import React, { InputHTMLAttributes } from 'react';
import { IconBaseProps } from 'react-icons';
import { Container } from './styles';

interface InputProps extends InputHTMLAttributes<HTMLInputElement>{
    name: string;
    icon: React.ComponentType<IconBaseProps>;
}

const Input: React.FC<InputProps> = ({ icon: Icon, ...rest }) => (
  <Container>
    {Icon && <Icon size={20} />}
    <input {...rest} />
  </Container>
);

export default Input;
```

* Após a tipagem da função, como vamos utilizar as propriedades que o Input usa (`type`, `name` etc), vamos tipar ela com uma interface criada chamada `InputProps`.
* A interface criada precisa receber como herança a interface do Input, com todas as propriedades. Para isso usamos uma interface do próprio react chamada `InputHTMLAttributes`. Precisamos também passar como parâmetro dessa interface o elemento HTML que iremos utilizar, no caso a `HTMLInputElement`.
* Para capturar o ícone, caso exista, além das propriedades do Input, inserimos uma propriedade `icon` na interface, que tem a tipagem `React.ComponentType<IconBaseProps>`.
* Usamos a desestruturação de objetos retirando o Icon, e todas as outras propriedades com o spread operator.
* Caso o Ícone seja informado, inserimos a tag.
* As propriedades  são informadas no input com o mesmo spread operator.


O `styles.ts` não afeta em nada o código, mas segue abaixo:

```JSX
import styled from 'styled-components';

export const Container = styled.div`
    background: #232129;
    border-radius: 10px;
    border: 2px solid #232129;
    padding: 16px;
    width: 100%;
    display: flex;
    align-items: center;
    color: #666360;
    input{
        color: #F4EDE8;
        flex: 1;
        border: 0;
        background: transparent;
        &::placeholder{
            color: #666360;
        }
    }
    svg{
        margin-right: 16px;
    }
    & + div {
        margin-top: 8px;
    }
`;
```
