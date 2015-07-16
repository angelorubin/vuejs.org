title: Primeiros Passos
type: guide
order: 2
---

## Introdução

Vue.js é uma biblioteca para construir aplicações web modernas.

Tecnicamente, o Vue.js é focado na camada [ViewModel](#ViewModel) do padrão MVVM. O ViewModel é responsável por conectar a [View](#View) e o [Model](#Model) através de um mecanismo chamado <i>two way data binding</i>. Manipulações no DOM  e a formatação das saídas são abstraídas utilizando [Diretivas](#Diretivas) e [Filtros](#Filtros).

Filosoficamente, o objetivo é fornecer benefícios do <i>reactive data binding</i> e componentes que possam ser combinados, baseados em uma API que seja o mais simples possível. Não é um framework completo - é desenvolvido para ser a camada <i>view</i>, sendo simples e flexível. Você pode utilizar ele somente para uma rápida prototipagem, ou combinar com outras bibliotecas para obter uma <i>stack de front end</i> customizada. Também se encaixa para serviços <i>no-backend</i> como o Firebase.

A API do Vue.js é fortemente influenciada pelos frameworks e bibliotecas [AngularJS], [KnockoutJS], [Ractive.js] e [Rivets.js]. Colocando de lado as similaridades, eu acredito que o Vue.js oferece uma valiosa alternativa à essas bibliotecas por se encaixar em um lugar entre a simplicidade e a funcionalidade.

Mesmo que você já esteja familiarizado com alguns desses termos, é recomendado que você acompanhe essa breve revisão dos conceitos, porque sua noção desses termos pode ser diferente do que eles significam no contexto do Vue.js.

## Visão Geral sobre Conceitos

### ViewModel

Um objeto que sincroniza o <i>Model</i> e a <i>View</i>. No Vue.js, cada instância do Vue é um ViewModel. Eles são instanciados com construtor do `Vue` ou suas sub-classes:

```js
var vm = new Vue({ /* options */ })
```

Esse é o primeiro objeto que você irá interagir como um desenvolvedor que utiliza o Vue.js. Para mais detalhes, veja [O Construtor Vue](/api/).

### View

O DOM que é gerenciado pela instância do Vue.

```js
vm.$el // The View
```

O Vue.js usa um template baseado no DOM. Cada instância do Vue é associada com um elemento correspondente no DOM. Quando uma instância do Vue é criada, ela percorre recursivamente todos os nós filhos do seu elemento pai enquanto configura o <i>data binding</i> necessário. Depois que a View é compilada, ela começa a ser reativa às mudanças nos dados.

Ao utilizar o Vue.js, você raramente terá que modificar o DOM por si mesmo, exceto ao usar diretivas customizadas (será explicado posteriormente). As atualizações na View ocorreção automaticamente quando os dados forem modificados. Essas atualizações da view são tão específicas que podem chegar ao nível de um <i>textNode</i>. Elas também são agrupadas e executadas assincronamente para uma melhor performance.

### Model

Um objeto javascript levemente modificado.

```js
vm.$data // The Model
```

No Vue.js, <i>models</i> são apenas objetos Javascript simples, ou **data objects**. Você pode manipular as propriedades deles e as instâncias do Vue que estarão observando esses objetos serão notificados das mudanças. O Vue.js alcança uma reatividade transparente convertendo as propriedades em <i>data objects</i> utilizando getter/setters do ES5. Não há necessidade para checagens mais "poluídas", e você também não precisa chamar explicitamente o Vue para atualizar a View. Não importa quando os dados mudam, a View sempre será atualizada no próximo frame.

Vue instances proxy all properties on data objects they observe. So once an object `{ a: 1 }` has been observed, both `vm.$data.a` and `vm.a` will return the same value, and setting `vm.a = 2` will modify `vm.$data`.

The data objects are mutated in place, so modifying it by reference has the same effects as modifying `vm.$data`. This makes it possible for multiple Vue instances to observe the same piece of data. In larger applications it is also recommended to treat Vue instances as pure views, and externalize the data manipulation logic into a more discrete store layer.

One caveat here is that once the observation has been initiated, Vue.js will not be able to detect newly added or deleted properties. To get around that, observed objects are augmented with `$add`, `$set` and `$delete` methods.

### Diretivas

Prefixed HTML attributes that tell Vue.js to do something about a DOM element.

```html
<div v-text="message"></div>
```

Here the div element has a `v-text` directive with the value `message`. This tells Vue.js to keep the div's textContent in sync with the Vue instance's `message` property.

Diretivas can encapsulate arbitrary DOM manipulations. For example `v-attr` manipulates an element's attributes, `v-repeat` clones an element based on an Array, `v-on` attaches event listeners... we will cover them later.

### Mustache Bindings

You can also use mustache-style bindings, both in text and in attributes. They are translated into `v-text` and `v-attr` directives under the hood. For example:

```html
<div id="person-{{id}}">Hello {{name}}!</div>
```

Although it is convenient, there are a few things you need to be aware of:

<p class="tip">The `src` attribute on an `<image>` element makes an HTTP request when a value is set, so when the template is first parsed it will result in a 404. In this case `v-attr` is preferred.</p>

<p class="tip">Internet Explorer will remove invalid inline `style` attributes when parsing HTML, so always use `v-style` when binding inline CSS if you want to support IE.</p>

You can use triple mustaches for unescaped HTML, which translates to `v-html` internally:

``` html
{{{ safeHTMLString }}}
```

However, this can open up windows for potential XSS attacks, therefore it is suggested that you only use triple mustaches when you are absolutely sure about the security of the data source, or pipe it through a custom filter that sanitizes untrusted HTML.

Finally, you can add `*` to your mustache bindings to indicate a one-time only interpolation, which does not react to data changes:

``` html
{{* onlyOnce }}
```

### Filtros

Filtros are functions used to process the raw values before updating the View. They are denoted by a "pipe" inside directives or bindings:

```html
<div>{{message | capitalize}}</div>
```

Now before the div's textContent is updated, the `message` value will first be passed through the `capitalize` function. For more details see [Filtros in Depth](/guide/filters.html).

### Components

In Vue.js, every component is simply a Vue instance. Components form a nested tree-like hierarchy that represents your application interface. They can be instantiated by a custom constructor returned from `Vue.extend`, but a more declarative approach is registering them with `Vue.component(id, constructor)`. Once registered, they can be declaratively nested in other Vue instance's templates in the form of custom elements:

``` html
<my-component>
  <!-- internals handled by my-component -->
</my-component>
```

This simple mechanism enables declarative reuse and composition of Vue instances in a fashion similar to [Web Components](http://www.w3.org/TR/components-intro/), without the need for latest browsers or heavy polyfills. By breaking an application into smaller components, the result is a highly decoupled and maintainable codebase. For more details, see [Component System](/guide/components.html).

## A Quick Example

``` html
<div id="demo">
  <h1>{{title | uppercase}}</h1>
  <ul>
    <li
      v-repeat="todos"
      v-on="click: done = !done"
      class="{{done ? 'done' : ''}}">
      {{content}}
    </li>
  </ul>
</div>
```

``` js
var demo = new Vue({
  el: '#demo',
  data: {
    title: 'todos',
    todos: [
      {
        done: true,
        content: 'Learn JavaScript'
      },
      {
        done: false,
        content: 'Learn Vue.js'
      }
    ]
  }
})
```

**Result**

<div id="demo"><h1>&#123;&#123;title | uppercase&#125;&#125;</h1><ul><li v-repeat="todos" v-on="click: done = !done" class="&#123;&#123;done ? 'done' : ''&#125;&#125;">&#123;&#123;content&#125;&#125;</li></ul></div>
<script>
var demo = new Vue({
  el: '#demo',
  data: {
    title: 'todos',
    todos: [
      {
        done: true,
        content: 'Learn JavaScript'
      },
      {
        done: false,
        content: 'Learn Vue.js'
      }
    ]
  }
})
</script>

Also available on [jsfiddle](http://jsfiddle.net/yyx990803/yMv7y/).

You can click on a todo to toggle it, or you can open your Browser's console and play with the `demo` object - for example, change `demo.title`, push a new object into `demo.todos`, or toggle a todo's `done` state.

You probably have a few questions in mind now - don't worry, we'll cover them soon.

Next up: [Diretivas in Depth](/guide/directives.html).

[AngularJS]: http://angularjs.org
[KnockoutJS]: http://knockoutjs.com
[Ractive.js]: http://ractivejs.org
[Rivets.js]: http://www.rivetsjs.com
