# Salvar arquivos no storage usando useEffect do ReactJS

Para salvar dados no localStorage com useEffect, suponha a seguinte situação.

Temos uma aplicação todoList, que armazena as tarefas no array `todoList` (array declarado com `useState`). Toda vez que uma variável for inserida nesse array, inserimos ela no localStorage:

Para isso, vamos usar o `useEffect` passando como parâmetro `watch` (variável que é assistida e toda vez que é alterada a função é executada) o array `todoList`:

```javascript
useEffect(() => {
    localStorage.setItem('@TodoListAPP::list',
    JSON.stringify(todoList)
    );
}, [todoList]);
```

Ou seja, toda vez que a variável `todoList` for alterada, a função `useEffect` atribui ao item `@TodoListAPP::list` (Nome do item feito para evitar conflitos com o storage de outras aplicações web) todo o array em forma de string.

Agora, toda vez que a página for carregada pela primeira vez, precisamos capturar os valores do localStorage e atribuir na variável `todoList`.

Para isso, ao declarar a variável `todoList` com `useState`, ao invés de passar um array vazio como parâmetro, passamos uma arrow function para capturar os valores do localStorage ou retornar um array vazio caso não encontre.

Além disso, nossa variável `todoList` é tipada com uma interface `List` criada na aplicação. Passamos ela no `useState` também:

```javascript
const [todoList, setTodoList] = useState<List[]>(()=> {
    const storagedItems = localStorage.getItem('@TodoListAPP::list');
    if (storagedItems){
        return JSON.parse(storagedItems);
    }else{
        return [];
    }
});
```