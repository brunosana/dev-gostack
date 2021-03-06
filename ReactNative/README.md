## ReactNative Notes

### Tópicos:

* React Native
* Arquitetura React Native
* Expo
* Criando Projeto React Native
* Diferenças do ReactJS
* Listando Projetos da API
* Criando novos projetos

## React Native

> React Native é a versão do React para desenvolvimento mobile. Ele cuida de qualquer parte do desenvolvimento de Interfaces.

---

* É multiplataforma, um único código roda no android e no ios, com estilizações e APIs diferentes.
* Manipula cada plataforma de forma diferente (Ex: IOS de um jeito Android de outro).
* É de interface NATIVA, ou seja, ele não produz webview, perdendo performance. Ele pega todo o código feito em JavaScript e injeta uma dependência JavaScriptPort fazendo com que o aparelho consiga ler JS.
* Vem ganhando força com grandes empresas como Microsoft.

---

* O código JavaScript passa pelo *Metro Bundler*, que manipula os códigos JS (como o webpack) e gera o bundle.js contendo todo o código da aplicação, passando para a *Bridge*, ponte entre o JavaScript e o código nativo da plataforma, se adaptando em plataformas como o IOS e Android.
* **Não usamos HTML** e sim *tags específicas* em React Native. (A tag *<view>* é como se fosse uma <div> etc.)
* Nenhum elemento tem estilo, todo elemento precisa ser estilizado.
* Usamos o CSS com uma biblioteca Yoga, que converte estilo CSS em estilo ObjectiveC e Java (para IOS e Android)

### Expo

* SDK com um conjunto de funcionalidades prontas para usar no ReactNative (Camera, Video, Interação com Facebook, Google etc)
* Não é necessário configurar emulador, instalamos o Expo na plataforma e ele interpreta o código.
* **Não é viável** pois não é possível mexer no código nativo, e com isso várias bibliotecas não tem suporte ao Expo. Portanto, não vale a pena o seu Uso.

## Criando Projeto React Native

Para configurar sua máquina (Seja com emulador ou dispositivo físico) só seguir os passos [desse link](https://react-native.rocketseat.dev/)

Para criar um projeto React Native, basta inserir `react-native init NOME_DO_PROJETO` no terminal.

Irá gerar uma estrutura de pastas:

```
- react-native-project-folder
  - _tests_
  - android
  - ios
  - node_nodules
  - .buckconfig
  - .flowconfig
  - .gitattributes
  - .gitignore
  - .wachmanconfig
  - app.json
  - babel.config.js
  - index.js
  - metro.config.js
  - package.json
```

Pode dar erro ao rodar o app no dispositivo físico, caso o erro seja `react native Could not determine the dependencies of task ':app:installDebug'` e/ou `SDK location not found. Define location with an ANDROID_SDK_ROOT environment variable or by setting the sdk.dir path in your project's local properties file at`, siga os passos abaixo:

1. Na pasta `android` do projeto crie um arquivo `local.properties`
2. Se for linux/mac, insira: `sdk.dir = /Users/USUARIO/Library/Android/sdk`
  * Se for Windows, insira: `sdk.dir = C:/Users/USUARIO/AppData/Local/Android/sdk`

Agora basta executar `npm start` em um terminal e `react-native run-android` em outro terminal (Na pasta do projeto).

---

**Por boas práticas de programação. Excluimos o arquivo `App.js` e criamos um arquivo em `src/index.js` contendo:

```javascript
import React from 'react';
import { View } from 'react-native';

export default function App(){
    return <View />;
}
```

Agora para consertar o erro da aplicação com o arquivo excluído, trocamos a importação do arquivo `index.js` na raiz do projeto:

```javavscript
import App from './src';
```

## Diferenças do ReactJS

> Os elementos e a estilização são diferentes no ReactJS e no React Native.

Como não vamos usar HTML não usamos tags como `<div>`, `<h1>` etc. Usamos tags exportadas do package `react-native`, como a `<View>` que é um tipo de container, que pode ser usado no lugar de `div`, `footer`, `header`, `section` etc.

Não possuem valor semântico (significado). Ex. No HTML a tag `footer` é um rodapé, no React-Native nenhum elemento tem significado, são usados de forma abstrata. Então da mesma forma, a tag do React-Native `<Text>` funciona semelhante ao span, strong `h1`, `h2`, `h3`, `p` etc.

Não possuem estilização própria. Ex. uma tag H1 fica em negrito, cor preta geralmente etc. H2, H3, P etc. Em React-Native precisamos da estilização.

A parte do CSS é feita dentro do JavaScript como código javascript

**OBS: TODOS OS ELEMENTOS POR PADRÃO POSSUEM `display: flex`**

NÃO HÁ HERANÇA DE ESTILOS NO REACT-NATIVE. Em um container tas TAGs filhas não irão receber modificações, portanto:

Também podemos alterar propriedades da statusbar (a barra de cima).

Ex. Em src/index.js importe além da View, o Text, o StyleSheet e o StatusBar:

```javascript
import React from 'react';
import { View, Text, StyleSheet, StatusBar } from 'react-native';
export default function App(){
    return (
        <>
        <StatusBar barStyle="light-content" backgroundColor="#7159c1" />
        <View style={styles.container}>
            <Text style={styles.title} >Hello World</Text>
        </View>
        </>
    );
}
const styles = StyleSheet.create({
    container: {
        flex: 1,
        backgroundColor: '#7159c1',
        justifyContent: 'center',
        alignItems: 'center'
    },
    title: {
        color: '#FFF',
        fontSize: 20,
        fontWeight: 'bold',
    }
})
```

## Listando Projetos da API

Não há diferenças entre ReactJS e React Native na parte de conexão, entretanto há diferenças entre o dispositivo físico do IOS/Android e Emulador IOS/Android. Para garantir a funcionalidade em todos usaremos o IP da máquina. Então podemos começar instalando também o axios com `yarn add axios` e criando o arquivo `src/services/api.js`:

```javacsript
import axios from 'axios';
const api = axios.create({
    baseURL: 'http://192.168.0.14'
})
export default api;
```

Nosso arquivo `src/index.js` ficará:

```javascript
import React, { useEffect, useState } from 'react';
import { SafeAreaView, FlatList, Text, StyleSheet, StatusBar } from 'react-native';
import api from './services/api';
export default function App(){
    const [projects, setProjects] = useState([]);

    useEffect(()=>{
        api.get('/projects').then(response=>{
            setProjects(response.data);
            console.log(response.data);
        })
    }, [])
    return (
        <>
        <StatusBar barStyle="light-content" backgroundColor="#7159c1" />
        <SafeAreaView style={styles.container}>
        <FlatList
            data={projects}
            keyExtractor={project => project.id}
            renderItem={({ item: project })=>(
                <Text style={styles.project}>{project.title}</Text>
            )}
        />
        </SafeAreaView>
        </>
    );
}
const styles = StyleSheet.create({
    container: {
        flex: 1,
        backgroundColor: '#7159c1'
    },
    project: {
        color: '#FFF',
        fontSize: 20,
        fontWeight: 'bold',
    }
})
```

* Não importamos mais a `View` pois agora usamos a `SafeAreaView`. Pois como estamos trabalhando com vários dados ela enquadra na safe.
* Usamos a tag FlatList pois ela é muito performatica que gerar vários view. E quando a quantidade de projetos impressa passa da tela ela cria um scrow

Sobre os parâmetros da FlatList:

* **data** -> Variável onde estão alocados os items que vão ser impressos.
* **keyExtractor** -> Propriedade da variável única que entrará no parânetro `key` da tag PAI em cada item repetido.
* **renderItem** -> É o que irá ser renderizado por Item. No caso, os parâmetros que a função envia são vários (Como se o item a ser impresso é impar, se é o primeiro, último etc), portanto usamos a desestruturação para capturar apenas o parâmetro Item e usamos um recurso do JS para renomear a variável para project.

## Criando novos projetos

Para criar um novo projeto, usaremos a mesma função do ReactJS:

```javascript
async function handleAdProject(){
    const response = await api.post('/projects', {
        title: `Projeto Novo ${Date.now()}`,
        desc: 'another desc'
    });
    setProjects([...projects, response.data])
}
```

Para adicionar um botão, importamos junto a `View`, `Flatlist` e `Text` o `TouchableOpacity`, poderíamos usar o `Button` mas ele já vem com estilização própria.

Portanto, abaixo da Flatlist inserimos:

```javascript
<TouchableOpacity
activeOpacity={0.6}
style={styles.button}
onPress={handleAdProject}
>
    <Text style={styles.buttonText}>Adicionar Projeto</Text>
</TouchableOpacity>
```

Propriedades:

* **activeOpacity**: Essa tag cria um botão que ao clicar reduz a sua opacidade, estamos controlando para quando ele for ativado sua opacidade ficar em 60%
* **style**: estilo aplicado
* **onPress**: Não temos a propriedade `onClick` no React-Native, usamos no lugar a `onPress`.
* Dentro da Tag inserimos um texto

Quanto aos estilos, adicionamos os dois novos:

```javascript
button:{
    backgroundColor: '#FFF',
    margin: 20,
    height: 50,
    borderRadius: 4,
    justifyContent: 'center',
    alignItems: 'center'
},
buttonText: {
    fontWeight: 'bold',
    fontSize: 16
}
```
