title: Métodos e Manipulação de Eventos
type: guide
order: 9
---

## Manipulando Eventos

Nós podemos usar a diretiva `v-on` para "ouvir" eventos do DOM:

``` html
<div id="example">
  <button v-on:click="greet">Greet</button>
</div>
```

Nós estamos vinculando um evento de clique para um método chamado `greet`. E é assim que definiremos aquele método na nossa instância Vue:

``` js
var vm = new Vue({
  el: '#example',
  data: {
    name: 'Vue.js'
  },
  // Definimos nossos métodos dentro do objeto `methods`
  methods: {
    greet: function (event) {
      // `this` dentro dos métodos se referencia à instância Vue.
      alert('Hello ' + this.name + '!')
      // `event` é o evento nativo do DOM
      alert(event.target.tagName)
    }
  }
})

// você pode invocar métodos JavaScript também!
vm.greet() // -> 'Hello Vue.js!'
```

Teste você mesmo:

{% raw %}
<div id="example" class="demo">
  <button v-on:click="greet">Greet</button>
</div>
<script>
var vm = new Vue({
  el: '#example',
  data: {
    name: 'Vue.js'
  },
  // definindo um método dentro do objeto `methods`
  methods: {
    greet: function (event) {
      // `this` dentro de métodos aponta para a vm.
      alert('Hello ' + this.name + '!')
      // `event` é o evento nativo do DOM
      alert(event.target.tagName)
    }
  }
})
</script>
{% endraw %}

## Manipulando Declarações Encadeadas

Ao invés de vincular diretamente ao nome de um método, nós também podemos utilizar declarações JavaScript encadeadas:

``` html
<div id="example-2">
  <button v-on:click="say('hi')">Say Hi</button>
  <button v-on:click="say('what')">Say What</button>
</div>
```
``` js
new Vue({
  el: '#example-2',
  methods: {
    say: function (msg) {
      alert(msg)
    }
  }
})
```

Resultado:
{% raw %}
<div id="example-2" class="demo">
  <button v-on:click="say('hi')">Say Hi</button>
  <button v-on:click="say('what')">Say What</button>
</div>
<script>
new Vue({
  el: '#example-2',
  methods: {
    say: function (msg) {
      alert(msg)
    }
  }
})
</script>
{% endraw %}

Assim como as limitações das expressões, os manipuladores de eventos estão restritos à **somente uma declaração**.

Algumas vezes nós também precisamos acessar o evento DOM original durante a manipulação do evento. Você pode passá-lo para o método utilizando a variável especial `$event`:

``` html
<button v-on:click="say('hello!', $event)">Submit</button>
```

``` js
// ...
methods: {
  say: function (msg, event) {
    // agora nós temos acesso ao evento nativo
    event.preventDefault()
  }
}
```

## Modificadores de Eventos

Ao lidar com eventos, é muito comum você utilizar métodos como o `event.preventDefault()` ou `event.stopPropagation()` para modificar o comportamento do evento. Apesar de poder fazer isso facilmente dentro do método, seria ainda melhor pudéssemos lidar somente com a lógica que estamos desenvolvendo, ao invés de lidar com detalhes dos eventos do DOM.

Para lidar com esse problema, o Vue.js fornece dois **modificadores de evento** para a diretiva `v-on`: `.prevent` e `.stop`. Lembre-se que modificadores são sufixos de diretivas, e representados com um ponto:

``` html
<!-- a propagação do evento Click será parada -->
<a v-on:click.stop="doThis"></a>

<!-- o evento de submit não irá mais recarregar a página -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- os modificadores podem ser encadeados -->
<a v-on:click.stop.prevent="doThat">
```

## Modificadores de Teclas

Ao lidar com eventos do teclado, geralmente precisamos verficar o código da tecla digitada. O Vue.js também permite que você adicione o um modificador para essa situação na diretiva `v-on`:

``` html
<!-- somente chame o método vm.submit() quando o código da tecla for igual a 13 -->
<input v-on:keyup.13="submit">
```

Lembrar o código das teclas é algo trabalhoso, então o Vue.js fornece alguns pseudônimos para as teclas mais utilizadas:

``` html
<!-- idem ao exemplo anterior -->
<input v-on:keyup.enter="submit">

<!-- também funciona para abreviações -->
<input @keyup.enter="submit">
```

Aqui temos a lista completa com os pseudõnimos para as teclas:

- enter
- tab
- delete
- esc
- space
- up
- down
- left
- right

## Por que manipular eventos no HTML?

Você deve estar se perguntando se toda essa coisa de manipular os eventos não violam as regras de ouro sobre "Separação de conceitos". Fique tranquilo - todas as funções de manipulações e expressões do Vue.js estão estritamente ligadas ao <i>ViewModel</i> que está vinculado a sua <i>View</i>, isso não acarretará dificuldade na manutenção. Na verdade, existem vários benefícios ao utilizar o `v-on`:

1. Facilita a identificação das funções que lidam com os eventos no seu código JS só de olhar no código HTML.

2. Como você não tem que manualmente anexar os manipuladores de evento no seu JS, seu <i>ViewModel</i> pode ser composto apenas por lógica e sem manipulações do DOM. Isso faz com que ele seja mais fácil de ser testado.

3. Quando um <i>ViewModel</i> é destruído, todo código responsável por lidar com a manipulação do evento é destruído automaticamente. Você não precisa se preocupar em realizar essa limpeza.
