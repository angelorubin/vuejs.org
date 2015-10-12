title: Propriedades Computadas
type: guide
order: 5
---

Expressões dentro de templates são convenientes, mas elas deveriam ser utilizadas somente para operações. Templates são destinados para descrever a estrutura da sua <i>view</i>. Colocar muita lógica nos seus templates pode fazer que com eles fiquem inchados e com que a sua manutenção fique mais complicada. É por esse motivo que o Vue.js limita as expressões de ligação para somente uma expressão. Para qualquer lógica que precise de mais de uma linha, você deve utilizar uma **propriedade computada**.

### Exemplo Básico

``` html
<div id="example">
  a={{ a }}, b={{ b }}
</div>
```

``` js
var vm = new Vue({
  el: '#example',
  data: {
    a: 1
  },
  computed: {
    // Um getter computado
    b: function () {
      // `this` representa a instância da sua vm
      return this.a + 1
    }
  }
})
```

Resultado:

{% raw %}
<div id="example" class="demo">
  a={{ a }}, b={{ b }}
</div>
<script>
var vm = new Vue({
  el: '#example',
  data: {
    a: 1
  },
  computed: {
    b: function () {
      return this.a + 1
    }
  }
})
</script>
{% endraw %}

Aqui nós declaramos a propriedade computada `b`. A função que nós fornecemos será utilizada como um <i>getter</i> para a propriedade `vm.b`:

``` js
console.log(vm.b) // -> 2
vm.a = 2
console.log(vm.b) // -> 3
```

Você pode abrir o console e brincar com o exemplo. O valor de `vm.b` sempre dependerá do valor da propriedade `vm.a`.


Você pode vincular uma propriedade computada em seus templates exatamente como você faz com uma propriedade normal. O Vue é capaz de reconhecer que a propriedade `vm.b` depende de `vm.a`, então ele vai atualizar todos os valores que dependem de `vm.b` quando a propriedade `vm.a` mudar. E a melhor parte é que nós criamos essa dependência de uma forma absolutamente declarativa: a função <i>getter</i> da propriedade computada é pura e não tem efeitos colaterais, o que faz com que seja fácil para testar e compreender.

### Propriedades Computadas vs. $watch

A API do Vue.js fornece um método chamado`$watch` que permite você observar as mudanças dos dados na instância do Vue. Quando você tem algum dado que precisa ser atualizado baseado em outro dado, é tentador utilizar o `$watch` - especialmente se você estiver vindo de um background AngularJS. Entretanto, geralmente é uma melhor ideia você utilizar uma propriedade computada, ao invés de utilizar o callback imperativo do `$watch`. Considere esse exemplo:

``` html
<div id="demo">{{fullName}}</div>
```

``` js
var vm = new Vue({
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  }
})

vm.$watch('firstName', function (val) {
  this.fullName = val + ' ' + this.lastName
})

vm.$watch('lastName', function (val) {
  this.fullName = this.firstName + ' ' + val
})
```

O código acima é imperativo e repetitivo. Compare com sua versão que utiliza propriedades computadas:

``` js
var vm = new Vue({
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})
```

Muito melhor, não acha?

### <i>Setter</i> Computado

As propriedades computadas por padrão são somente para visualização, mas você pode criar um métodod <i>setter</i> quando precisar:

``` js
// ...
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...
```

Agora, quando você chamar `vm.fullName = 'John Doe'`, o <i>setter</i> será invocado e os valores de `vm.firstName` e `vm.lastName` serão atualizados de acordo.

Os detalhes técnicos por trás de como as propriedades computadas são atualizadas serão [discutidas em outra seção](reactivity.html#Inside_Computed_Properties) dedicada exclusivamente ao sistema de reatividade.
