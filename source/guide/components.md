title: Componentes
type: guide
order: 12
---

## Utilizando Componentes

### Registrando

Nós aprendemos nas seções anteriores que podemos criar um componente construtor utilizando `Vue.extend()`:

``` js
var MyComponent = Vue.extend({
  // options...
})
```

Para usar esse construtor como um componente, você precisa **registar-lo** com `Vue.component(tag, constructor)`:

``` js
// Registrando globalmente o componente com a tag: my-component
Vue.component('my-component', MyComponent)
```

Uma vez registrado, o componente agora pode ser utilizado no template de uma instância pai como um elemento personalizado, `<my-component>`. Verifique se o componente está **registrado** antes de você criar sua instância raiz Vue. Aqui está o exemplo completo:

``` html
<div id="example">
  <my-component></my-component>
</div>
```

``` js
// definir
var MyComponent = Vue.extend({
  template: '<div>A custom component!</div>'
})

// registrar
Vue.component('my-component', MyComponent)

// criar uma instância raiz
new Vue({
  el: '#example'
})
```

A qual será renderizada:

``` html
<div id="example">
  <div>A custom component!</div>
</div>
```

{% raw %}
<div id="example" class="demo">
  <my-component></my-component>
</div>
<script>
Vue.component('my-component', {
  template: '<div>A custom component!</div>'
})
new Vue({ el: '#example' })
</script>
{% endraw %}

Note que o template do componente **substitui** o elemento personalizado, o qual só serve como um **ponto de montagem**. Este comportamento pode ser configurado utilizando a opção `replace` da instância.

### Registrando Localmente

Você não tem que registrar todos os componentes globalmente. Você pode fazer um componente disponível apenas no âmbito de um outro componente, registrando-o com a opção `components` da instância:

``` js
var Child = Vue.extend({ /* ... */ })

var Parent = Vue.extend({
  template: '...',
  components: {
    // <my-component> só estarão disponíveis no template Parent
    'my-component': Child
  }
})
```

O mesmo encapsulamento se aplica para outros tipos de ativos, como as diretivas, filtros e transições.

### Registration Sugar

Para facilitar as coisas, você pode passar diretamente nas opções do objeto em vez de um construtor real para `Vue.component ()` e a opção `component`. Vue.js chamará automaticamente `Vue.extend ()` para você internamente:

``` js
// estender e registrar em uma única etapa
Vue.component('my-component', {
  template: '<div>A custom component!</div>'
})

// também funciona para registro local
var Parent = Vue.extend({
  components: {
    'my-component': {
      template: '<div>A custom component!</div>'
    }
  }
})
```

### Opções de Componentes - Ressalvas

A maioria das opções que podem ser passadas para o construtor Vue podem ser utilizadas em `Vue.extend ()`, com dois casos especiais: `data` e` el`. Imagine que simplesmente passamos um objeto como `data` para `Vue.extend ()`:

``` js
var data = { a: 1 }
var MyComponent = Vue.extend({
  data: data
})
```

O problema com isto é que o mesmo objeto `data` será compartilhado entre todas as instâncias de `MyComponent`! Isto provavelmente não é o que queremos, por isso, devemos utilizar uma função que retorna um novo objeto na opção `data`:

``` js
var MyComponent = Vue.extend({
  data: function () {
    return { a: 1 }
  }
})
```

A opção `el` também exige um valor na função quando usado em `Vue.extend ()`, exatamente pela mesma razão.

### atributo `is`

Alguns elementos HTML, por exemplo `<table>`, tem restrições sobre quais os elementos que podem aparecer no seu interior. Elementos personalizados que não estão na lista de permissões serão içados para fora e, portanto, não processados corretamente. Nesses casos, você deve usar o atributo especial `is` para indicar um elemento personalizado:

``` html
<table>
  <tr is="my-component"></tr>
</table>
```

## Props

### Passando Dados com Props

Cada instância do componente tem seu próprio **escopo isolado**. Isto significa que você não pode (e não deve) referenciar diretamente dados do pai no template de um componente filho. Os dados podem ser transmitidos a componentes filhos utilizando **props**.

A "prop" é um campo nos dados de um componente que é esperada para ser transmitida a partir do seu componente pai. Um componente filho precisa declarar explicitamente as props que espera receber usando o [opção `props`](/api/options.html#props):

``` js
Vue.component('child', {
  // declarar a props
  props: ['msg'],
  // a prop pode ser usada dentro de templates, e também será 
  // definida como `this.msg`
  template: '<span>{{ msg }}</span>'
})
```

Então, podemos passar uma string simples a ela assim:

``` html
<child msg="hello!"></child>
```

**Result:**

{% raw %}
<div id="prop-example-1" class="demo">
  <child msg="hello!"></child>
</div>
<script>
new Vue({
  el: '#prop-example-1',
  components: {
    child: {
      props: ['msg'],
      template: '<span>{{ msg }}</span>'
    }
  }
})
</script>
{% endraw %}

### camelCase versus kebab-case

HTML attributes are case-insensitive. When using camelCased prop names as attributes, you need to use their kebab-case (hyphen-delimited) equivalents:

Atributos HTML são case-insensitive. Quando utilizar nomes de propriedades camelCase como atributos, você precisa usar seus equivalentes kebab-case (delimitado por hífen):

``` js
Vue.component('child', {
  // camelCase no JavaScript
  props: ['myMessage'],
  template: '<span>{{ myMessage }}</span>'
})
```

``` html
<!-- kebab-case no HTML -->
<child my-message="hello!"></child>
```

### Dynamic Props
### Propriedades Dinâmicas

Similar to binding a normal attribute to an expression, we can also use `v-bind` for dynamically binding props to data on the parent. Whenever the data is updated in the parent, it will also flow down to the child:

``` html
<div>
  <input v-model="parentMsg">
  <br>
  <child v-bind:my-message="parentMsg"></child>
</div>
```

It is often simpler to use the shorthand syntax for `v-bind`:

``` html
<child :my-message="parentMsg"></child>
```

**Result:**

{% raw %}
<div id="demo-2" class="demo">
  <input v-model="parentMsg">
  <br>
  <child v-bind:my-message="parentMsg"></child>
</div>
<script>
new Vue({
  el: '#demo-2',
  data: {
    parentMsg: 'Message from parent'
  },
  components: {
    child: {
      props: ['myMessage'],
      template: '<span>{{myMessage}}</span>'
    }
  }
})
</script>
{% endraw %}

### Prop Binding Types

By default, all props form a **one-way-down** binding between the child property and the parent one: when the parent property updates, it will flow down to the child, but not the other way around. This default is meant to prevent child components from accidentally mutating the parent's state, which can make your app's data flow harder to reason about. However, it is also possible to explicitly enforce a two-way or a one-time binding with the `.sync` and `.once` **binding type modifiers**:

Compare the syntax:

``` html
<!-- default, one-way-down binding -->
<child :msg="parentMsg"></child>

<!-- explicit two-way binding -->
<child :msg.sync="parentMsg"></child>

<!-- explicit one-time binding -->
<child :msg.once="parentMsg"></child>
```

The two-way binding will sync the change of child's `msg` property back to the parent's `parentMsg` property. The one-time binding, once set up, will not sync future changes between the the parent and the child.

<p class="tip">Note that if the prop being passed down is an Object or an Array, it is passed by reference. Mutating the Object or Array itself inside the child **will** affect parent state, regardless of the binding type you are using.</p>

### Prop Validation

It is possible for a component to specify the requirements for the props it is receiving. This is useful when you are authoring a component that is intended to be used by others, as these prop validation requirements essentially constitute your component's API, and ensure your users are using your component correctly. Instead of defining the props as an array of strings, you can use the object hash format that contain validation requirements:

``` js
Vue.component('example', {
  props: {
    // basic type check (`null` means accept any type)
    propA: Number,
    // a required string
    propB: {
      type: String,
      required: true
    },
    // a number with default value
    propC: {
      type: Number,
      default: 100
    },
    // object/array defaults should be returned from a
    // factory function
    propD: {
      type: Object,
      default: function () {
        return { msg: 'hello' }
      }
    },
    // indicate this prop expects a two-way binding. will
    // raise a warning if binding type does not match.
    propE: {
      twoWay: true
    },
    // custom validator function
    propF: {
      validator: function (value) {
        return value > 10
      }
    }
  }
})
```

The `type` can be one of the following native constructors:

- String
- Number
- Boolean
- Function
- Object
- Array

In addition, `type` can also be a custom constructor function and the assertion will be made with an `instanceof` check.

When a prop validation fails, Vue will refuse to set the value on the child component, and throw a warning if using the development build.

## Parent-Child Communication

### Parent Chain

A child component holds access to its parent component as `this.$parent`. A root Vue instance will be available to all of its descendants as `this.$root`. Each parent component has an array, `this.$children`, which contains all its child components.

Although it's possible to access any instance the parent chain, you should avoid directly relying on parent data in a child component and prefer passing data down explicitly using props. In addition, it is a very bad idea to mutate parent state from a child component, because:

1. It makes the parent and child tightly coupled;

2. It makes the parent state much harder to reason about when looking at it alone, because its state may be modified by any child! Ideally, only a component itself should be allowed to modify its own state.

### Custom Events

All Vue instances implement a custom event interface that facilitates communication within a component tree. This event system is independent from the native DOM events and works differently.

Each Vue instance is an event emitter that can:

- Listen to events using `$on()`;

- Trigger events on self using `$emit()`;

- Dispatch an event that propagates upward along the parent chain using `$dispatch()`;

- Broadcast an event that propagates downward to all descendants using `$broadcast()`.

<p class="tip">Unlike DOM events, Vue events will automatically stop propagation after triggering callbacks for the first time along a propagation path, unless the callback explicitly returns `true`.</p>

A simple example:

``` html
<!-- template for child -->
<template id="child-template">
  <input v-model="msg">
  <button v-on:click="notify">Dispatch Event</button>
</template>

<!-- template for parent -->
<div id="events-example">
  <p>Messages: {{ messages | json }}</p>
  <child></child>
</div>
```

``` js
// register child, which dispatches an event with
// the current message
Vue.component('child', {
  template: '#child-template',
  data: function () {
    return { msg: 'hello' }
  },
  methods: {
    notify: function () {
      if (this.msg.trim()) {
        this.$dispatch('child-msg', this.msg)
        this.msg = ''
      }
    }
  }
})

// bootstrap parent, which pushes message into an array
// when receiving the event
var parent = new Vue({
  el: '#events-example',
  data: {
    messages: []
  },
  // the `events` option simply calls `$on` for you
  // when the instance is created
  events: {
    'child-msg': function (msg) {
      // `this` in event callbacks are automatically bound
      // to the instance that registered it
      this.messages.push(msg)
    })
  }
})
```

{% raw %}
<template id="child-template">
  <input v-model="msg">
  <button v-on:click="notify">Dispatch Event</button>
</template>

<div id="events-example" class="demo">
  <p>Messages: {{ messages | json }}</p>
  <child></child>
</div>
<script>
Vue.component('child', {
  template: '#child-template',
  data: function () {
    return { msg: 'hello' }
  },
  methods: {
    notify: function () {
      if (this.msg.trim()) {
        this.$dispatch('child-msg', this.msg)
        this.msg = ''
      }
    }
  }
})

var parent = new Vue({
  el: '#events-example',
  data: {
    messages: []
  },
  events: {
    'child-msg': function (msg) {
      this.messages.push(msg)
    }
  }
})
</script>
{% endraw %}

### v-on for Custom Events

The example above is pretty nice, but when we are looking at the parent's code, it's not so obvious where the `"child-msg"` event comes from. It would be better if we can declare the event handler in the template, right where the child component is used. To make this possible, `v-on` can be used to listen for custom events when used on a child component:

``` html
<child v-on:child-msg="handleIt"></child>
```

This makes things very clear: when the child triggers the `"child-msg"` event, the parent's `handleIt` method will be called. Any code that affects the parent's state will be inside the `handleIt` parent method; the child is only concerned with triggering the event.

### Child Component Refs

Despite the existence of props and events, sometimes you might still need to directly access a child component in JavaScript. To achieve this you have to assign a reference ID to the child component using `v-ref`. For example:

``` html
<div id="parent">
  <user-profile v-ref:profile></user-profile>
</div>
```

``` js
var parent = new Vue({ el: '#parent' })
// access child component instance
var child = parent.$.profile
```

When `v-ref` is used together with `v-for`, the ref you get will be an Array or an Object containing the child components mirroring the data source.

## Content Distribution with Slots

When using components, it is often desired to compose them like this:

``` html
<app>
  <app-header></app-header>
  <app-footer></app-footer>
</app>
```

There are two things to note here:

1. The `<app>` component do not know what content may be present inside its mount target. It is decided by whatever parent component that is using `<app>`.

2. The `<app>` component very likely has its own template.

To make the composition work, we need a way to interweave the parent "content" and the component's own template. This is a process called **content distribution** (or "transclusion" if you are familiar with Angular). Vue.js implements a content distribution API that is modeled after with the current [Web Components spec draft](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md), using the special `<slot>` element to serve as distribution outlets for the original content.

### Compilation Scope

Before we dig into the API, let's first clarify which scope the contents are compiled in. Imagine a template like this:

``` html
<child>
  {{ msg }}
</child>
```

Should the `msg` be bound to the parent's data or the child data? The answer is parent. A simple rule of thumb for component scope is:

> Everything in the parent template is compiled in parent scope; everything in the child template is compiled in child scope.

A common mistake is trying to bind a directive to a child property/method in the parent template:

``` html
<!-- does NOT work -->
<child v-show="someChildProperty"></child>
```

Assuming `someChildProperty` is a property on the child component, the example above would not work as intended. The parent's template should not be aware of the state of a child component.

If you need to bind child-scope directives on a component root node, you should do so in the child component's own template:

``` js
Vue.component('child-component', {
  // this does work, because we are in the right scope
  template: '<div v-show="someChildProperty">Child</div>',
  data: function () {
    return {
      someChildProperty: true
    }
  }
})
```

Similarly, distributed content will be compiled in the parent scope.

### Single Slot

Parent content will be **discarded** unless the child component template contains at least one `<slot>` outlet. When there is only one slot with no attributes, the entire content fragment will be inserted at its position in the DOM, replacing the slot itself.

Anything originally inside the `<content>` tags is considered **fallback content**. Fallback content is compiled in the child scope and will only be displayed if the hosting element is empty and has no content to be inserted.

Suppose we have a component with the following template:

``` html
<div>
  <h1>This is my component!</h1>
  <slot>
    This will only be displayed if there is no content
    to be distributed.
  </slot>
</div>
```

Parent markup that uses the component:

``` html
<my-component>
  <p>This is some original content</p>
  <p>This is some more original content</p>
</my-component>
```

The rendered result will be:

``` html
<div>
  <h1>This is my component!</h1>
  <p>This is some original content</p>
  <p>This is some more original content</p>
</div>
```

### Named Slots

`<slot>` elements have a special attribute, `name`, which can be used to further customize how content should be distributed. You can have multiple slots with different names. A named slot will match any element that has a corresponding `slot` attribute in the content fragment.

There can still be one unnamed slot, which is the **default slot** that serves as a catch-all outlet for any unmatched content. If there is no default slot, unmatched content will be discarded.

For example, suppose we have a `multi-insertion` component with the following template:

``` html
<div>
  <slot name="one"></slot>
  <slot></slot>
  <slot name="two"></slot>
</div>
```

Parent markup:

``` html
<multi-insertion>
  <p slot="one">One</p>
  <p slot="two">Two</p>
  <p>Default A</p>
</multi-insertion>
```

The rendered result will be:

``` html
<div>
  <p slot="one">One</p>
  <p>Default A</p>
  <p slot="two">Two</p>
</div>
```

The content distribution API is a very useful mechanism when designing components that are meant to be composed together.

## Dynamic Components

You can use the same mount point and dynamically switch between multiple components by using the reserved `<component>` element and dynamically bind to its `is` attribute:

``` js
new Vue({
  el: 'body',
  data: {
    currentView: 'home'
  },
  components: {
    home: { /* ... */ },
    posts: { /* ... */ },
    archive: { /* ... */ }
  }
})
```

``` html
<component :is="currentView">
  <!-- component changes when vm.currentview changes! -->
</component>
```

If you want to keep the switched-out components alive so that you can preserve its state or avoid re-rendering, you can add a `keep-alive` directive param:

``` html
<component :is="currentView" keep-alive>
  <!-- inactive components will be cached! -->
</component>
```

### `activate` Hook

When switching components, the incoming component might need to perform some asynchronous operation before it should be swapped in. To control the timing of component swapping, implement the `activate` hook on the incoming component:

``` js
Vue.component('activate-example', {
  activate: function (done) {
    var self = this
    loadDataAsync(function (data) {
      this.someData = data
      done()
    })
  }
})
```

Note the `activate` hook is only used for dynamic component swapping - it does not affect static components and manual insertions with instance methods.

### `transition-mode`

The `transition-mode` param attribute allows you to specify how the transition between two dynamic components should be executed.

By default, the transitions for incoming and outgoing components happen simultaneously. This attribute allows you to configure two other modes:

- `in-out`: New component transitions in first, current component transitions out after incoming transition has finished.

- `out-in`: Current component transitions out first, new component transitions in after outgoing transition has finished.

**Example**

``` html
<!-- fade out first, then fade in -->
<component
  :is="view"
  transition="fade"
  transition-mode="out-in">
</component>
```

``` css
.fade-transition {
  transition: opacity .3s ease;
}
.fade-enter, .fade-leave {
  opacity: 0;
}
```

{% raw %}
<div id="transition-mode-demo" class="demo">
  <input v-model="view" type="radio" value="v-a" id="a" name="view"><label for="a">A</label>
  <input v-model="view" type="radio" value="v-b" id="b" name="view"><label for="b">B</label>
  <component
    :is="view"
    transition="fade"
    transition-mode="out-in">
  </component>
</div>
<style>
  .fade-transition {
    transition: opacity .3s ease;
  }
  .fade-enter, .fade-leave {
    opacity: 0;
  }
</style>
<script>
new Vue({
  el: '#transition-mode-demo',
  data: {
    view: 'v-a'
  },
  components: {
    'v-a': {
      template: '<div>Component A</div>'
    },
    'v-b': {
      template: '<div>Component B</div>'
    }
  }
})
</script>
{% endraw %}

## Misc

### Authoring Reusable Components

When authoring components, it is good to keep in mind whether you intend to reuse this component somewhere else later. It is OK for one-off components to have some tight coupling with each other, but reusable components should define a clean public interface.

The API for a Vue.js component essentially comes in three parts - props, events and slots:

- **Props** allow the external environment to feed data to the component;

- **Events** allow the component to trigger actions in the external environment;

- **Slots** allow the external environment to insert content into the component's view structure.

With the dedicate shorthand syntax for `v-bind` and `v-on`, the intents can be clearly and succinctly conveyed in the template:

``` html
<my-component
  :foo="baz"
  :bar="qux"
  @event-a="doThis"
  @event-b="doThat">
  <!-- content -->
  <img slot="icon" src="...">
  <p slot="main-text">Hello!</p>
</my-component>
```

### Async Components

In large applications, we may need to divide the app into smaller chunks, and only load a component from the server when it is actually needed. To make that easier, Vue.js allows you to define your component as a factory function that asynchronously resolves your component definition. Vue.js will only trigger the factory function when the component actually needs to be rendered, and will cache the result for future re-renders. For example:

``` js
Vue.component('async-example', function (resolve, reject) {
  setTimeout(function () {
    resolve({
      template: '<div>I am async!</div>'
    })
  }, 1000)
})
```

The factory function receives a `resolve` callback, which should be called when you have retrieved your component definition from the server. You can also call `reject(reason)` to indicate the load has failed. The `setTimeout` here is simply for demonstration; How to retrieve the component is entirely up to you. One recommended approach is to use async components together with [Webpack's code-splitting feature](http://webpack.github.io/docs/code-splitting.html):

``` js
Vue.component('async-webpack-example', function (resolve) {
  // this special require syntax will instruct webpack to
  // automatically split your built code into bundles which
  // are automatically loaded over ajax requests.
  require(['./my-async-component'], resolve)
})
```

### Assets Naming Convention

Some assets, such as components and directives, appear in templates in the form of HTML attributes or HTML custom tags. Since HTML attribute names and tag names are **case-insensitive**, we often need to name our assets using kebab-case instead of camelCase, which can be a bit inconvenient.

Vue.js actually supports naming your assets using camelCase or PascalCase, and automatically resolves them as kebab-case in templates (similar to the name conversion for props):

``` js
// in a component definition
components: {
  // register using camelCase
  myComponent: { /*... */ }
}
```

``` html
<!-- use dash case in templates -->
<my-component></my-component>
```

This works nicely with [ES6 object literal shorthand](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer#New_notations_in_ECMAScript_6):

``` js
// PascalCase
import TextBox from './components/text-box';
import DropdownMenu from './components/dropdown-menu';

export default {
  components: {
    // use in templates as <text-box> and <dropdown-menu>
    TextBox,
    DropdownMenu
  }
}
```

### Fragment Instance

When you use the `template` option, the content of the template will replace the element the Vue instance is mounted on. It is therefore recommended to always include a single root-level element in templates.

There are a few conditions that will turn a Vue instance into a **fragment instance**:

1. Template contains multiple top-level elements.
2. Template contains only plain text.
3. Template contains only another component.
4. Template contains only an element directive, e.g. `<partial>` or vue-router's `<router-view>`.
5. Template root node has a flow-control directive, e.g. `v-if` or `v-for`.

The reason is that all of the above cause the instance to have an unknown number of top-level elements, so it has to manage its DOM content as a fragment. A fragment instance will still render the content correctly. However, it will **not** have a root node, and its `$el` will point to an "anchor node", which is an empty Text node (or a Comment node in debug mode).

What's more important though, is that **non-flow-control directives, non-prop attributes and transitions on the component element will be ignored**, because there is no root element to bind them to:

``` html
<!-- doesn't work due to no root element -->
<example v-show="ok" transition="fade"></example>

<!-- props work -->
<example :prop="someData"></example>

<!-- flow control works, but without transitions -->
<example v-if="ok"></example>
```

There are, of course, valid use cases for fragment instances, but it is in general a good idea to give your component template a single, plain root element. It ensures directives and attributes on the component element to be properly transferred, and also results in slightly better performance.

### Inline Template

When the `inline-template` special attribute is present on a child component, the component will use its inner content as its template, rather than treating it as distributed content. This allows more flexible template-authoring.

``` html
<my-component inline-template>
  <p>These are compiled as the component's own template</p>
  <p>Not parent's transclusion content.</p>
</my-component>
```

However, `inline-template` makes the scope of your templates harder to reason about, and makes the component's template compilation un-cachable. As a best practice, prefer defining templates inside the component using the `template` option.
