title: A Instância Vue
type: guide
order: 3
---

## Construtor

Todos os aplicativos Vue.js são iniciados com a construção de uma **instância Vue principal (raíz)** com a função construtora do `Vue`:

``` js
var vm = new Vue({
  // opções
})
```

Uma instância Vue é essenciamente um **ViewModel** como definido no [Padrão MVVM](https://en.wikipedia.org/wiki/Model_View_ViewModel), daí o nome da variável `vm` que você verá ao longo da documentação.

Quando você cria uma instância do Vue, você precisa passar um **objeto de opções** que contém suas opções para dados, template html, o elemento ao qual o Vue será vinculado, os métodos, ciclo de vida dos <i>callbacks</i> e mais. A lista completa de opções pode ser encontrada na referência da API.

O construtor do `Vue` pode ser extendido para criar **construtores de componentes** com opções pré-definidas:

``` js
var MyComponent = Vue.extend({
  // opções herdadas
})

// todas as instâncias do componente `MyComponent` serão criadas com
// as opções pré-definidas.
var myComponentInstance = new MyComponent()
```

Apesar da opção de criarmos instâncias extendidas imperativamente, na maioria dos casos nós estaremos registrando o construtor de um componente como um elemento personalizado e compondo ele em seus templates declarativamente. Nós falaremos mais ( e detalhadamente) sobre o sistema de componentes mais à frente. Por enquanto, você só precisa saber que todos os componentes Vue.js são essencialmente instâncias do Vue extendidas.

## Métodos e Propriedades

Cada instância Vue **intercepta** todas as propriedades encontradas no seu objeto `data`:

``` js
var data = { a: 1 }
var vm = new Vue({
  data: data
})

vm.a === data.a // -> true

// definindo a propriedade também afeta o seu valor original
vm.a = 2
data.a // -> 2

// ... e vice-versa
data.a = 3
vm.a // -> 3
```

Deve ser notado que somente essas propriedades interceptadas são **reativas**. Se você adicionar uma nova propriedade para a instância depois dela ter sido criada, ela não emitirá nenhum update para a <i>view</i>. Nós discutiremos o sistema de reatividade com detalhes no futuro.

Em adição às propriedades de dados, as instãncias do Vue expoem vários métodos e propriedades úteis. Essas propriedades e métodos são prefixados com o símbolo `$` para diferenciá-las das propriedades de dados que são interceptadas. Por exemplo:

``` js
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})

vm.$data === data // -> true
vm.$el === document.getElementById('example') // -> true

// $watch é um método da instância
vm.$watch('a', function (newVal, oldVal) {
  // esse callback será chamado quando o valor da propriedade `vm.a` for modificado
})
```

Consulte o guia da API para uma lista completa das propriedades e métodos.

## Ciclo de Vida da Instância

Cada instância do Vue passa por uma série de passos de inicialização quando é criada - por exemplo, ela precisa inicializar a observação dos dados, compilar o template e criar os vínculos necessários. Durante esse procedimento, ela também invocará alguns **"métodos" do ciclo de vida**, que nos dá a oportunidade de executar uma lógica customizada. Por exemplo, o método `created` é chamado após a instância ser criada.

``` js
var vm = new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` aponta para a instância do Vue
    console.log('a is: ' + this.a)
  }
})
// -> "a is: 1"
```

Também existem métodos que são chamados em diferentes estágios do ciclo de vida da instãncia, como por exemplo os métodos `compiled`, `ready`e `destroyed`. Todos os métodos do ciclo de vida são chamados com sua propriedade `this` apontando para a instância Vue que o invocou. Alguns usuários podem estar se perguntando onde o conceito de <i>controllers</i> estão no mundo do Vue.js, e a resposta é: não existem <i>controllers</i> no Vue.js. Toda sua lógica customizada para um componente deve ser divididas nesses métodos do ciclo de vida.

## Diagrama do Ciclo de Vida de uma Instância

Abaixo desse texto temos um diagrama para o ciclo de vida de uma instância Vue. VOcê não precisa entender tudo que está acontecendo nesse momento, mas esse diagrama certamente vai lhe ajudar no futuro.

![Lifecycle](/vuejs.org/images/lifecycle.png)
