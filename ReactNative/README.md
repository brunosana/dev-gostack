## ReactNative Notes

### Tópicos:

* React Native
* Arquitetura React Native
* Expo

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