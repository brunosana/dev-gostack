# Hook useCallBack

Os conceitos de estado e imutabilidade do React fazem com que sempre que um componente é modificado, ele é recriado e novamente renderizado.

Isso traz um problema quando tratamos os componentes no React como função, pois as funções dentro dos componentes são novamente recriadas na memória. Isso é um problema, perdemos performance desse jeito.

Para resolver isso, podemos usar um Hook do React chamado `useCallback`.

Vamos ver uma situação sem usar o Hook:

```JSX
import React, { useState } from 'react';

const Component: React.FC = () => {
    const [message, setMessage] = useState('Hello World');

    function handleChangeMessage(){
        setMessage('You Clicked!');
    }

    return (
        <h1>{message}</h1>
        <button onClick={handleChangeMessage} type="button">Change message</button>
    );
}

export default Component;
```

O `useCallBack` é uma forma de declarar funções que não são recriadas na memória toda vez que o componente atualiza. Elas ficam salvas na memória, e seus carregamentos são controlados.

O método recebe dois parâmetros, assim como o useEffect, ele recebe além da função, as variáveis que vão ser monitoradas e as que sofrerem alteração, o método é recriado.

Caso não passemos nenhum parâmetro, o `useCallback` nunca irá recriar a função.

Nosso arquivo final ficará:

```JSX
import React, { useState, useCallback } from 'react';

const Component: React.FC = () => {
    const [message, setMessage] = useState('Hello World');

    const handleChangeMessage = useCallback(
    () => {
        setMessage('You Clicked!');
    }, []);

    return (
        <h1>{message}</h1>
        <button onClick={handleChangeMessage} type="button">Change message</button>
    );
}

export default Component;
```