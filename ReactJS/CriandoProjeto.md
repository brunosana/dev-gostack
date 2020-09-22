## Criando Projetos ReactJS

### Tópicos:

* Criando projetos

## Criando projetos

Para otimizar a criação de projetos, utilizamos o `create-react-app`, que já tem toda a parte do Babel, Webpack configurado com vários Loaders. É uma estrutura semi pronta para não precisar configurar tudo do zero.

Como estaremos desenvolvendo em Typescript, passaremos uma flag informando:

```
create-react-app <nome_do_projeto> --template=typescript
```

Como essa estrutura já cria uma aplicação funcional, alguns arquivos criados com ela são desnecessários pois vamos criar do zero. Para isso, em `src` exclua os seguintes arquivos:

* `App.css`
* `App.test.tsx`
* `index.css`
* `logo.svg`
* `serviceWorker.ts`

Agora precisamos limpar todos os locais onde esses arquivos eram utilizados, então remova as importações em:

* `index.tsx` - Css, ServiceWorker e a última linha
* `app.tsc` - Logo - Css - e retornando apenas uma tag qualquer (h1 por exemplo)

E em `public`, remova:

* `favicon.ico`
* `logo192.png`
* `logo512.png`
* `manifest.json`

Em `index.html`, a partir do `<meta name="description"...` remova até a tag `title`. Também remova a referência ao `favicon.ico`.

## Scripts

Junto da criação do projeto, o package.json contém alguns scripts:

* `start` - Inicia o servidor local
* `build` - Prepara a aplicação para produção
* `test` - Inicia o processo de testes
* `eject` - Traz todas as configurações do webpack, babel etc (o que está em `react-scripts`) para ser modificado da forma que a gente quer. Não é recomendado pois raramente será necessário o seu uso e não tem volta depois de feito.

![Padrões de Projeto](https://github.com/brunosana/dev-gostack/blob/master/NodeJS/PadroesEDebugNodeJS.md)