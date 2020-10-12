# Validando formulários com Yup e Unform

Para verificações de Email, NOme e vários outros dados de um formulário, usamos o `yup`.

Começamos com `yarn add yup` e `yarn add @types/yup -D`.

Podemos importar várias coisas do Yup. Seja validação por validação (validação de email, de nome, de password etc) ou importamos tudo em uma única variável.

Nesse caso, vamos importar tudo do pacote atribuindo a uma variável `Yup`:

```JSX
import * as Yup from 'yup';
```

Um boa forma de fazer é criando um schema de validação, que fazemos quando queremos validar um objeto inteiro, informando que os dados estarão no formato de Objeto. E usamos o método shape para definir a forma do objeto:

```JSX
  const handleSubmit = useCallback(
    async (data: object) => {
      try {
        const schema = Yup.object().shape({
          name: Yup.string().required('Nome obrigatório'),
          email: Yup.string().required('Email obrigatório').email('Digite um e-mail válido'),
          password: Yup.string().min(6, 'Senha de no mínimo 6 dígitos'),
        });
        await schema.validate(data, {
          abortEarly: false,
        });
      } catch (err) {
        console.log(err);
      }
    }, [],
  );
```

* Colocamos todo o código dentro de um bloco de try catch para garantir que todo erro que acontecer seja tratado pela aplicação.
* Informamos o tipo que cada campo terá com o Yup (A [documentação](https://github.com/jquense/yup) pode ajudar).
* O método `required` informa que o campo é obrigatório, e enviamos uma string com o erro caso este seja satisfeito.
* O método `email` informa que o campo é um email, e caso haja erro nessa validação, enviamos a string com o erro para ser capturada.
* No password o método `min` garante que no mínimo a senha possua 6 dígitos, enviamos como segundo parâmetro a string caso seja menos que 6 o erro seja retornado.
* Transformamos a função em assíncrona e usamos o método `validade` para fazer a validação dos dados, passando o nosso objeto como parâmetro.
* No segundo parâmetro, enviamos algumas configurações para o Yup
  * o `abortEarly` definimos como `false` pois o seu valor padrão é dado como `true`, ele faz com que assim que ele encontre um erro, ele interrompa a validação, porém vamos desligar isso para mostrar todos os erros.

## Exibindo os erros no Input

Como estamos utilizando o Unform, existe uma técnica para informar aos componentes isolados sobre eventuais erros. Para isso, iremos usar o `useRef` para capturar os elementos de forma direta!

Porém, como estamos lidando com o `useRef`, temos um problema de tipagem pois a variável começa como `null`.

Para isso, vamos importar a interface que contém todas as tipagens do `@unform/core` com:

```JSX
import { FormHandles } from '@unform/core';
```

Na página do form, inserimos:

```JSX
const formRef = useRef<FormHandles>(null);

[...]
<Form ref={formRef} {...}>
</Form>

```

* Veja que também atribuimos a referência na tag com ref={formRef}.

E com a referência setada, podemos ter acesso a todos os métodos que a tag `Form` do package *unform* tem.

Agora, quando dá erro na nossa requisição podemos capturar a variável dentro do catch:

```JSX
catch (err) {
  console.log(err);
}
```

Essa variável `err` contém, dentre outras propriedades, a `inner`, que irá conter todos os objetos com erros. E nesse objeto, temos acesso a várias propriedades, incluindo o `path`, que contém o componente que gerou o erro, o tipo de erro etc.. Temos também o `message` que contém a mensagem de erro.

Para cada erro, criamos um objeto com o formato tendo o `name` e o `message`.

Vamos então criar um arquivo em `src/utils/getValidationErrors.ts`.

```JSX
import { ValidationError } from 'yup';

interface Errors{
    [key: string]: string;
}

export default function getValidationErrors(err: ValidationError): Errors {
  const validationErrors: Errors = {};
  err.inner.forEach((error) => {
    validationErrors[error.path] = error.message;
  });
  return validationErrors;
}
```


OBS: Vamos lembrar que o `setErrors` podemos passar o `name` como propriedade, que seria equivalente à propriedade `name` atribuida ao `input`. Ou seja, como essa função será utilizada em outros lugares, podemos ter email, name, password, profile_photo e vários outros campos que poderão estar ou não em um determinado formulário.

Para resolver esse problema, nós falamos que a interface que retorna os errors possui uma `[key: string]: string;`, significa que o nome da chave, com essa sintaxe, pode ser qualquer string, fazendo com que não haja um nome específico para a propriedade a ser retornada.

Fazemos uma varredura em todo o objeto `err`, capturando cada erro e atribuindo na variável `validationErrors`, retornando este objeto ao final com todos os erros.

Então, no `catch`, vamos usar a o `getValidationErrors` que criamos:

```JSX
} catch (err) {
  const errors = getValidationErrors(err);
  formRef.current?.setErrors(errors);
}
```

Agora, conseguimos capturar e atribuir os erros de forma dinâmica e genérica.

Depois de capturados os erros, ao fazer uma nova verificação, precisamos limpar os erros novamente, para que o estado deles se mantenha neutro enquando verificando. Para isso, logo depois do início do bloco try, inserimos a linha `formRef.current?.setErrors({});` e limpamos os erros.

Precisamos então exibir os erros no input.

# Criando Tooltip de erros

No final do component `Input`, inserimos antes do final da tag `Container` o trecho `{error}`. Esse trecho é do `unform` que usamos nos estudos passados.

E precisamos estilizar nossa tag error. Para isso, é simples, no começo da tag container inserimos uma propriedade `isErrored={!!error}`, e inserimos ela na interface de parâmetros que nosso `Component` recebe com `isErrored: boolean;`.

Podemos então estilizar:

```JSX
${(props) => props.isErrored && css` color: #C53030;`}
```

Vamos mudar agora para inserir o ícone ao invés do texto, e quando passar o mouse sobre o ícone mostra o erro.

Para isso, vamos alterar a linha que exibe o erro de `{error}` para:

```JSX
{error && <Error><FiAlertCircle color="#c53030" size={20} /></Error> }
```

A estilização do Error será:

```JSX
export const Error = styled.div`
    height: 20px;
    margin-left: 16px;
    svg{ margin: 0; }
`
```

Ok, precisamos criar agora o Tooltip de Errors, usando os conceitos de Isolated components, pois iremos usar em mais de um componente.

Criamos um novo componente em `src/components/Tooltip/index.tsx`:

```JSX
import React from 'react';
import { Container } from './styles';

interface TooltipProps{
    title: string;
}

const Tooltip: React.FC<TooltipProps> = ({ title, children }) => (
  <Container>
    {children}
    <span>{title}</span>
  </Container>
);

export default Tooltip;
```

Com o tooltip criado, podemos importar ele em nosso `Input`.

Como nós criamos uma tag `Error` em nosso `Input`, podemos referenciar toda a estilização do `Error` para o `Container` do `Tooltip`.

```JSX
import Tooltip from '../Tooltip';
[...]
export const Error = styled(Tooltip)`
    height: 20px;
    margin-left: 16px;
    svg{ margin: 0; }
`;
```

Com isso, estamos enviando toda a estilização da tag `Error` para o `Container` do `Tooltip`, há alguns detalhes que precisam ser levados em consideração:

1. No styled-components as referências são passadas como classes (é possível observar esse comportamento inspencionando o elemento na página web final ou em desenvolvimento no browser).
2. Precisamos informar ao arquivo de estilização do `Tooltip` que ele poderá receber estilização.

Para isso em `Tooltip/index;tsx`:

```JSX
{...}
interface TooltipProps{
    title: string;
    className?: string;
}

const Tooltip: React.FC<TooltipProps> = ({ title, className, children }) => (
  <Container className={className}>
    {children}
    <span>{title}</span>
  </Container>
);
{...}
```

Inserimos a propriedade `className` em `TooltipProps` e atribuimos no Container.

Feito isso em `Input/index.tsx` encontramos um erro, pois na Tag `Error` não contém a prorpiedade `title` (É obrigatória segundo a `TooltipProps`):

```JSX
<Error title={error}>
  <FiAlertCircle color="#c53030" size={20} />
</Error>
```

Executando a aplicação já é possível observar o ícone com a mensagem de erro!

Podemos então estilizar o nosso Tooltip em `Tooltip/styles.ts`:

```JSX
import styled from 'styled-components';

export const Container = styled.div`
    position: relative;
    span{
        width: 160px;
        background: #FF9000;
        padding: 8px;
        border-radius: 4px;
        font-size: 14px;
        font-weight:  500;
        opacity: 0;
        transition: opacity 0.4s;
        visibility: hidden;
        position: absolute;
        bottom: calc(100% + 12px);
        left: 50%;
        transform: translateX(-50%);
        color: #312e38;

        &::before{
            content: '';
            border-style: solid;
            border-color: #FF9000 transparent;
            border-width: 6px 6px 0 6px;
            top: 100%;
            position: absolute;
            left: 50%;
            transform: translateX(-50%);
        }
    }
    &:hover span{
        opacity: 1;
        visibility: visible;
    }
`;
```

A cor padrão está laranja, pois é um Tooltip genérico, como no formulário estamos lidando com erros, é bom deixar na cor de Erro (um vermelho).

Para isso, vamos estilizar a tag `Error`, foi para isso que fizemos todo o processo anteirormente. Agora podemos ter um componente isolado, que receba propriedades de acordo com a necessidade da apliação.

Para isso, em `Input/styles.ts`:

```JSX
export const Error = styled(Tooltip)`
    height: 20px;
    margin-left: 16px;
    svg{ margin: 0; }
    span{
        background: #C53030;
        color: #fff;
        &::before{
            border-color: #c53030 transparent;
        }
    }
`;
```
