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

As instâncias do Vue interceptarão e observarão todas as propriedades nos <i>data objects</i>. Então a partir do momento em que um objeto `{ a: 1 }` está sendo observado, tanto `vm.$data.a` quanto `vm.a` retornarão o mesmo valor, e ao setar um valor como `vm.a = 2` também modificará o valor de `vm.$data`.

Os <i>data objects</i> são modificados em um único local, então modificá-lo via referência tem o mesmo efeito que modificar `vm.$data`. Isso faz possível com que múltiplas instâncias do Vue observem um mesma porção de dados. Em grandes aplicações também é recomendado tratar instâncias do Vue como simples <i>views</i>, assim externalizando a lógica na manipulação dos dados para um layer de armazenamento mais discreto.

Uma ressalva aqui é que uma vez a observação inicializada, o Vue.js não será capaz de detectar novas propriedades ou propriedades removidas. Para lidar com isso, objetos observados contém os métodoos `$add`, `$set` e `$delete`.

### Diretivas

Atributos HTML prefixados que indicam para o Vue que naquele elemento do DOM alguma ação deve ser tomada.

```html
<div v-text="message"></div>
```

Nesse exemplo a div possui uma diretiva `v-text` com o valor `message`. Isso dirá ao Vue.js para manter o textContent dessa div em sincronia com a propriedade `message` de sua instância.

Diretivas podem encapsular manipulações arbitrárias no DOM. Por exemplo, a diretiva `v-attr` manipula os atributos de um elemento, a diretiva `v-repeat` clona um elemento baseado em um Array, e a diretiva `v-on` inclui <i>event listeners</i>... nós falaremos disso depois.

### Mustache Bindings

 Você também pode usar <i>mustache-style bindings</i>. Eles serão traduzidos para as diretivas `v-text` e `v-attr` directives under the hood. For example:

```html
<div id="person-{{id}}">Hello {{name}}!</div>
```

Apesar disso ser conveniente, existem alguns pontos que você precisa saber:

<p class="tip">O atributo `src` no elemento `<image>` faz uma requisição HTTP quando um valor é atribuido, então na primeira renderização do template resultará em um erro 404. Nesse caso, o uso da diretiva `v-attr` é recomendada.</p>

<p class="tip">O Internet Explorer removerá todos `styles` inline inválidos dos atributos quando converter o HTML, então sempre utilize a diretiva `v-style` ao utilizar CSS inline se você desejar suporte ao IE.</p>

Você pode utilizar três chaves para exibir HTML "não escapado (inseguro)", que será traduzido para a diretiva `v-html` internamente:

``` html
{{{ safeHTMLString }}}
```

Entretanto, isso pode abrir brechas para potenciais ataques XSS, então só é sugerido utilizar três chaves quando você tem absoluta certeza sobre a segurança da origem dos seus dados, ou então utilize um filtro que sanitize seus códigos HTML que não são seguros.

Finalmente, você pode adicionar um `*` ao seu <i>mustache binding</i> para indicar que essa é uma interpolação única, e que não será reativa para mudanças:

``` html
{{* onlyOnce }}
```

### Filtros

Filtros são funções utilizadas para processar valores "crus" antes de atualizar a View. Eles são denotados com um "pipe" dentro de diretivas ou bindings:

```html
<div>{{message | capitalize}}</div>
```

Agora antes que o textContent da div seja atualizado, o valor de `message` primeiro passará pelo filtro `capitalize`. Par mais detalhes sobre filtros confira [Tudo sobre Filtros](/guide/filters.html).

### Components

No Vue.js, todo componente é uma simples instância do Vue. Componentes formam um conjunto de hierarquias "baseados em árvores" que representam a interface da sua aplicação. Eles podem ser instanciados por um construtor customizado, que é retornado pelo método `Vue.extend`, porém uma maneira mais declarativa de fazer isso é registrá-lo utilizando o `Vue.component(id, constructor)`. Uma vez registrado, eles podem ser afixados em outras instâncias do Vue na forma de elementos customizados.

``` html
<my-component>
  <!-- internals handled by my-component -->
</my-component>
```

Esse simples mecanismo possibilita o reuso e a composição de instâncias do Vue de forma parecida com [Web Components](http://www.w3.org/TR/components-intro/), sem a necessidade dos mais recentes ou de polyfills robustos. Ao separar uma aplicação em componentes menores, o resultado é uma base de código altamente desacoplada e com alta manutenibilidade. Para mais detalhes, acesse [Sistema de Componentes](/guide/components.html).

## Um breve exemplo

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

**Resultado**

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

Confira esse código no [jsfiddle](http://jsfiddle.net/yyx990803/yMv7y/).

Você pode clicar em um todo para alternar seu estado, ou você pode abrir o console de seu navegador e brincar com o objeto `demo` - por exemplo, mude `demo.title`, e adicione um novo objeto à `demo.todos`, ou alterne um status de <i>done</i> de um todo.

Você provavelmente possui algumas questões agora - não se preocupe, nós responderemos elas em breve.

Próximo capítulo: [Tudo sobre Diretivas](/guide/directives.html).

[AngularJS]: http://angularjs.org
[KnockoutJS]: http://knockoutjs.com
[Ractive.js]: http://ractivejs.org
[Rivets.js]: http://www.rivetsjs.com
