title: Renderização de Listas
type: guide
order: 8
---

## v-for

Nós podemos utilizar a diretiva `v-for` para renderizar uma lista de itens baseados em um array. A diretiva `v-for` precisa de uma sintaxe especial, dessa maneira: `item in items`, onde `items` é o array com os dados e `item` é um **apelido** para o elemento que está sendo percorrido nesse momento:

**Example:**

``` html
<ul id="example-1">
  <li v-for="item in items">
    {{ item.message }}
  </li>
</ul>
```

``` js
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

**Resultado:**

{% raw %}
<ul id="example-1" class="demo">
  <li v-for="item in items">
    {{item.message}}
  </li>
</ul>
<script>
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  },
  watch: {
    items: function () {
      smoothScroll.animateScroll(null, '#example-1')
    }
  }
})
</script>
{% endraw %}

Dentro dos blocos de um `v-for` nós temos acesso total ao escopo "pai" e às suas propriedades, além de uma variável especial chamada `$index`, que, como você provavelmente já imagina, é o índex do array para o item atual:

``` html
<ul id="example-2">
  <li v-for="item in items">
    {{ parentMessage }} {{ $index }} {{ item.message }}
  </li>
</ul>
```

``` js
var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Parent',
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

**Resultado:**

{% raw%}
<ul id="example-2" class="demo">
  <li v-for="item in items">
    {{ parentMessage }} - {{ $index }} - {{ item.message }}
  </li>
</ul>
<script>
var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Parent',
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  },
  watch: {
    items: function () {
      smoothScroll.animateScroll(null, '#example-2')
    }
  }
})
</script>
{% endraw %}

## Template e v-for

Assim como o template do `v-if`, você pode usar uma tag `<template>` com um `v-for` para renderizar um bloco com múltiplos elementos. Por exemplo:

``` html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider"></li>
  </template>
</ul>
```

## Detectando mudanças no Array

### Métodos Modificadores

O Vue.js encapsula um array que é observado com métodos modificadores, e então eles fazem o trabalho de ativar mudanças na <i>view</i>. Os métodos encapsulados são:

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

Você pode abrir o console e brincar com o exemplo anterior. Modifique o array `items` chamando algum dos métodos modificadores. Por exemplo: `example1.items.push({ message: 'Baz' })`.

### Substituindo um Array

Os métodos modificadores, como seu nome sugere, modificam o array original em que são chamados. Em comparação, também existem métodos "não-modificadores", como por exemplo os métodos `filter()`, `concat()` e `slice()`, que não modificam o array original, porém **sempre retornar um novo array**. Quando você estiver trabalhando com métodos "não-modificadores", você pode simplesmente substituir o array antigo pelo novo:

``` js
example1.items = example1.items.filter(function (item) {
  return item.message.match(/Foo/)
})
```

Você pode pensar que isso fará com que o Vue.js jogue fora o DOM existente e renderize novamente a lista antiga - felizmente não é esse o caso. O Vue.js implementa algumas técnicas de heurística para maximizar o reuso dos elementos do DOM, então substituir um array com outros que contenham objetos sobrepostos é uma operação muito eficiente.

### track-by

Em alguns casos, você talvez precise substituir um array com objetos totalmente diferentes - um exemplo é um retorno de uma API. Como o `v-for` determina a reusabilidade de escopos e elementos DOM existentes a partir da identificação deles no objeto de dados, isso pode causar uma nova renderização de todos itens da lista. Entretanto, se cada um dos seus objetos possuir um identificador único entre as propriedades, então você pode utilizar o atributo especial `track-by` para dar uma dica ao Vue.js de como reutilizar as instâncias existentes na lista.

Por exemplo, se seus dados se parecerem com esses:

``` js
{
  items: [
    { _uid: '88f869d', ... },
    { _uid: '7496c10', ... }
  ]
}
```

Então você pode rastreá-los assim:

``` html
<div v-for="item in items" track-by="_uid">
  <!-- conteúdo -->
</div>
```

Mais a frente, quando você substituir o array `items` e o Vue.js encontrar um novo objeto com o `_uid: '88f869d'`, ele saberá que pode reutilizar o escopo e o elemento DOM associado com o mesmo `_uid`.

### track-by $index

Se você não possui um identificador único para rastrear o item, você pode utilizar a opção `track-by="$index"`, que irá forçar o `v-for` a realizar uma atualização in-loco: os fragmentos não serão mais reorganizados, eles simplesmete serão atualizados com o novo valor que corresponda ao `$index` em questão. Desse modo você pode lidar também com valores duplicados no seu array de dados.

Isso pode fazer com que a substituição de arrays seja extremamente eficiente, mas tem um lado negativo. Como os nós do DOM não são mais movidos para refletir as mudanças, os estados temporários do DOM como os valores de input e o estado privado de um componente podem ser dessincronizados. Então, seja muito cuidadoso ao utilizar o `track-by="$index"` se o seu bloco `v-for`conter inputs ou componentes filhos.

### Ressalvas

Por causa das limitações do JavaScript, o Vue.js **não pode** detectar as seguintes mudanças em um array:

1. Quando você diretamente seta um item à um index, exemplo: `vm.items[0] = {}`;
2. Quando você modifica o tamanho de um array, exemplo: `vm.items.length = 0`.

Par alidar com o primeiro item, o Vue.js lhe dá a opção de ampliar um array com o método `$set()`:

``` js
// assim como `example1.items[0] = ...` mas faz com que a view seja atualizada
example1.items.$set(0, { childMsg: 'Changed!'})
```

Para lidar com o segundo item, simplesmente substitua `items` com um array vazio.

Além do método `$set()`, o Vue.js também fornece um método chamado `$remove()`, que varre o array e remove um item chamando o método `splice()` internamente. Então, ao invés de:

``` js
var index = this.items.indexOf(item)
if (index !== -1) {
  this.items.splice(index, 1)
}
```

Você pode simplesmente fazer isso:

``` js
this.items.$remove(item)
```

## Objeto v-for

Você também pode utilizar um `v-for` para iterar as propriedades de um objeto. Assim como o `$index`, cada escopo terá acesso à uma nova propriedade chamada `$key`.

``` html
<ul id="repeat-object" class="demo">
  <li v-for="value in object">
    {{ $key }} : {{ value }}
  </li>
</ul>
```

``` js
new Vue({
  el: '#repeat-object',
  data: {
    object: {
      FirstName: 'John',
      LastName: 'Doe',
      Age: 30
    }
  }
})
```

**Resultado:**

{% raw %}
<ul id="repeat-object" class="demo">
  <li v-for="value in object">
    {{ $key }} : {{ value }}
  </li>
</ul>
<script>
new Vue({
  el: '#repeat-object',
  data: {
    object: {
      FirstName: 'John',
      LastName: 'Doe',
      Age: 30
    }
  }
})
</script>
{% endraw %}

<p class="tip">Ao iterar um objeto, a ordem é baseada de acordo com a função `Object.keys()`, que **não** pode ter sua consistência garantida em todas as implementações da engine JavaScript.</p>

## v-for e números

`v-for` também aceita como parâmetro um valor inteiro. Nesse caso, nós vamos repetir o template X vezes.

``` html
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
```

Resultado:

{% raw %}
<div id="range" class="demo">
  <span v-for="n in 10">{{ n }} </span>
</div>
<script>
new Vue({ el: '#range' })
</script>
{% endraw %}

## Exibindo Resultados Filtrados/Ordenados

Algumas vezes nós queremos exibir apenas uma versão dos dados filtrada ou em uma ordem específica sem ter que modificar ou resetar o array original. Existem duas opções para alcançar esse resultado:

1. Criar uma propriedade computada que retorne o valor filtrado ou ordenado do array;
2. Utilizar os filtros padrões `filterBy` e `orderBy`.

Uma propriedade computada lhe daria um controle mais maleável (um ajuste fino) e mais flexibilidade, já que é totalmente JavaScript; mas os filtros podem ser mais convenientes para usos comuns. Para ver o uso detalhados de filtros de Array, verifique sua [documentação](/api/filters.html#filterBy).
