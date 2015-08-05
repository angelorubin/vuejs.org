title: API Global
type: api
order: 5
---

### Vue.config

`Vue.config` é um objeto que contém as configurações globais do Vue. Abaixo está uma lista de todas as configurações disponíveis e seus valores padrão:

``` js
{
  // habilita o modo debug. Veja abaixo para mais detalhes.
  debug: true,
  // habilita o modo estrito. Veja abaixo para mais detalhes.
  strict: false,
  //o prefixo usado nas diretivas
  prefix: 'v-',
  // delimitadores para expressões Vue.
  // Para interpolação HTML é automaticamente
  // adicionado um caracter extra
  delimiters: ['{{', '}}'],
  // suprimir warnings?
  silent: false,
  // interpolar mustache bindings?
  interpolate: true,
  // utilizar atualizações assíncronas (para diretivas e watchers)?
  async: true,
  // permitir alteração na cadeia dos Arrays observados?
  proto: true
}
```
Você pode modifica-las diretamente, como por exemplo:

``` js
Vue.config.debug = true // habilita o modo debug
```

**Modo Debug**

Quando `Vue.config.debug` está setado como _true_, o Vue irá:

1. Imprimir stack traces para todos os warnings.
2. Deixar todos os nós visíveis no DOM como comentários. Isso torna mais fácil inspecionar a estrutura do que foi renderizado.

<p class="tip">o modo debug não está disponível na versão minificada, pronta para produção.</p>

**Modo Estrito**

Por padrão os componentes Vue herdam recursos tanto da cadeia de herança de classe (via `Vue.extend`) como dos componentes pai na hierarquia de view. No modo estrito, os componentes apenas poderão herdar recursos da cadeira de herança de classe, mas NÃO dos seus pais na hierarquia de views. Quando o modo estrito está habilitado, os recursos devem ou ser registradas de forma global ou depender explicitamente do componente que precisa deles. Quando aplicado pode resultar num melhor encapsulamento e reusabilidade em projetos maiores.

**Modificando Delimitadores**

Quando os delimitadores são setados para fazer a interpolação de texto, os delimitadores para interpolação HTML serão gerados com um símbolo a mais em ambos os lados:

``` js
Vue.config.delimiters = ['(%', '%)']
// agora as tags são (% %) para texto
// e ((% %)) para HTML
```

### Vue.extend( opções )

- **opções** `Objeto`

Cria uma "sub-classe" do construtor Vue. Todas as [opções de instanciação](/api/options.html) podem ser utilizadas. Os casos especiais a serem mencionados são `el` e `data`, que neste caso precisam ser funções.

Internamente `Vue.extend()` é chamado em todas as opções de componente antes destes serem instanciados. Para mais detalhes sobre componentes, veja [Sistema de Componentes](/guide/components.html).

**Exemplo**
``` html
<div id="mount-point"></div>
```

``` js
// cria um construtor reutilizável
var Profile = Vue.extend({
  template: '<p>{{nome}} {{sobrenome}} conhecido como {{apelido}}</p>'
})
// cria uma instância de Profile
var profile = new Profile({
  data: {
    nome : 'Walter',
    sobrenome : 'White',
    apelido  : 'Heisenberg'
  }
})
// finalmente monta no elemento HTML
profile.$mount('#mount-point')
```

O que resultará em:

``` html
<p>Walter White conhecido como Heisenberg</p>
```

### Vue.nextTick( callback )

- **callback** `Função`

Adia a execução do método de callback para depois do ciclo de atualização do DOM. Use imediatamente a ter alterado alguma informação que precisa aguardar por uma atualização no DOM. Para mais detalhes veja [Compreendendo Atualizações Assíncronas](/guide/directives.html#Understanding_Async_Updates).

### Vue.directive( id, [definição] )

- **id** `String`
- **definição** `Função` ou `Objeto` *opcional*

Registra ou obtém uma definição global de efeito de transição em JavaScript. Para mais detalhes veja o guia para [Transições JavaScript](/guide/transitions.html#JavaScript_Only_Transitions).

### Vue.elementDirective( id, [definição] )

- **id** `String`
- **definição** `Função` ou `Objeto` *opcional*

Registra ou obtém uma diretiva de elemento customizado. Para mais detalhes ver [Diretivas de Elemento](/guide/custom-directive.html#Element_Directives).

### Vue.filter( id, [definição] )

- **id** `String`
- **definição** `Função` *opcional*

Registra ou obtém um filtro customizado global. Para mais detalhes ver [Escrevendo Filtros Customizados](/guide/custom-filter.html).

### Vue.component( id, [definição] )

- **id** `String`
- **definição** `Function Constructor` ou `Objeto` *opcional*

Registra ou obtém um componente global. Para mais detalhes ver [Sistema de Componentes](/guide/components.html).

### Vue.transition( id, [definição] )

- **id** `String`
- **definição** `Objeto` *opcional*

Registra ou obtém uma difinição de efeito de transição JavaScript. Para mais detalhes ver o guia para [Transições Javascript](/guide/transitions.html#JavaScript_Functions).

### Vue.partial( id, [partial] )

- **id** `String`
- **partial** `String` *opcional*

Registra ou obtém uma string global de fragmento de template (partials). Para mais detalhes ver [Fragmentos de Templates (Partials)](/api/elements.html#partial)

### Vue.use( plugin, [args...] )

- **plugin** `Objeto` ou `Função`
- **args...** *opcional*

Monta um plugin Vue.js. Se o *plugin* é um Objeto, este deve obrigatoriamente conter um método chamado `install`. Se é uma função, esta será tratada como o método `install`. Este método será chamado como um argumento. Para mais detalhes veja [Plugins](/guide/extending.html#Extend_with_Plugins).

### Vue.util

Expõe o módulo interno `util` que contém uma quantidade de método utilitários. Ele é geralmente utilizado para criação avançada de plugins/diretivas. Para utiliza-lo você terá que olhar o código fonte para ter conhecimento do que está a disposição.
