# Usando Unform

Quando usamos formulários, para cada input, temos um estado, que a cada alteração ele é recriado. Isso causa uma perda de performance na aplicação. Para isso usamos uma biblioteca criada pela Rocketseat chamada Unform!

Começamos instalando as dependências com `yarn add @unform/core @unform/web`

E em seu componente, podemos importar:

```JSX
import { Form } from '@unform/web';
```

E podemos substituir a tag `form` que estamos usando no componente por `Form`.

Podemos agora informar quais os campos o Unform irá monitorar o valor (Podemos chamar de Registro do campo).

Para isso, considerando que estamos usando o conceito de *Isolated Components*, vamos até o componente e importamos o `useField` do `@unform/core` com `import { useField } from '@unform/core';`.

Também vamos usar do React o `useEffect` e o `useRef`!

O `useRef` cria uma referência para um elemento no React, para poder ser acessado diretamente.

```JSX
const Input: React.FC<InputProps> = ({ name, icon: Icon, ...rest }) => {
  const inputRef = useRef(null);
  const {
    fieldName, defaultValue, error, registerField,
  } = useField(name);

  useEffect(() => {
    registerField({
      name: fieldName,
      ref: inputRef.current,
      path: 'value',
    });
  }, [fieldName, registerField]);

  return (
    <Container>
      {Icon && <Icon size={20} />}
      <input defaultValue={defaultValue} ref={inputRef} {...rest} />
    </Container>
  );
};
```

1. Criamos uma variável para receber a referência, chamamos de `inputRef` e ele irá ser inicializada como `null`.
2. Chamamos a função `useField` passando o `name` como parâmetro (desestruturamos nos parâmetros da função).
  1. O primeiro elemento é o `fieldName`, é usado para capturarmos o name, pois o unform pode mudar o `name` original do elemento baseado em algumas condições
  2. defaultValue
  3. error
  4. registerField - Função que irá registrar o campo para ser observado no unform
3. Com o `useEffect`, assim que a nossa aplicação é inicializada ele invoca a função `useField` para fazer o registro passando alguns parâmetros de configurações.
  1. name: Nome do campo, passamos o `fieldName`
  2. ref: Referência do elemento, usaremos a `inputRef.current` para capturar o input
  3. path: qual o caminho que o useField irá percorrer para capturar o valor da tag, como estamos lidando com inputs, usamos `value`
4. atribuimos a referência na tag com o trecho `ref={inputRef}`
5. Passamos o `defaultValue` como parâmetro da tag Input. Ele permite que a gente atribua um valor inicial à variável

Podemos então, usar a propriedade `onSubmit` para verificar se os dados estão capturados:

```JSX
function handleSubmit(data: object): void{
    console.log(data);
}

{...}

<Form initialData={{ name: 'Bruno' }}  onSubmit={handleSubmit}>

{...}

</Form>
```

* O trecho `initialData={{ name: 'Bruno' }}` insere no input com a propriedade `name="name"` inicialmente o valor "Bruno".