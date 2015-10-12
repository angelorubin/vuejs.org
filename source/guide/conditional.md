title: Renderização Condicional
type: guide
order: 7
---

## v-if

Em templates de texto puro, por exemplo, <i>Handlebars</i>, nos poderíamos escrever um bloco condicional como essse:

``` html
<!-- Handlebars template -->
{{#if ok}}
  <h1>Yes</h1>
{{/if}}
```

Com Vue.js, nós usamos a diretiva `v-if` para atingir o mesmo resultado:

``` html
<h1 v-if="ok">Yes</h1>
```

Também é possível adicionar um bloco "else" com o `v-else`:

``` html
<h1 v-if="ok">Yes</h1>
<h1 v-else>No</h1>
```

## Template v-if

Como o `v-if` é uma diretiva, ele deve ser adicionado a um único elemento. Mas e se a gente quiser alternar mais de um elemento? Nesse caso nós podemos utilizar o `v-if` em um elemento `<template>`, que funcionaria como um container invisível. O conteúdo renderizado final não será incluído no elemento `<template>`.

``` html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

## v-show

Outra opção para exibir um elemento condicionalmente é a diretiva `v-show`. O seu uso é praticamente o mesmo:

``` html
<h1 v-show="ok">Hello!</h1>
```

A diferença é que um elemento com a diretiva `v-show` sempre será renderizado e permanecerá no DOM; O `v-show` sempre alterna a propriedade CSS `display` do elemento

Note que o `v-show` não suporta a sintaxe em conjunto com o `<template>`.

## v-else

Você pode adicionar uma diretiva `v-else` para indicar um "bloco else" para o `v-if` ou o `v-show`:

``` html
<div v-if="Math.random() > 0.5">
  Sorry
</div>
<div v-else>
  Not sorry
</div>
```

O elemento `v-else` precisa ser o primeiro elemento após o `v-if` ou `v-show` - caso contrário ele não será reconhecido.

## v-if vs. v-show

Quando o status de um `v-if` é alternado, o Vue.js terá que realizar um processo de compilação/destruição parcial, porque o template dentro do `v-if` pode conter <i>data bindings</i> ou componentes filhos. O `v-if` é uma renderização condicional "real" porque ele garante que os observadores de eventos e componentes filhos dentro do bloco condicional sejam propriamente destruídos e recriados durante as alternâncias de valor do `v-if`.

`v-if` também é **preguiçoso**: se a condição for falsa na renderização inicial, ele não fará nada - a compilação parcial não será iniciada até que a condição se torne verdade pela primeira vez (e a compilação passa a ser realizava via cache subsequentemente).

Em comparação, o `v-show` é muito mais simples - o elemento sempre é compilado e preservado, simplesmente com a troca de uma propriedade CSS.

Em termos gerais, a alternância das renderizações de um `v-if` possui um custo maior para sua aplicação, enquanto o `v-show` irá custar mais na renderização inicial. Então prefira o uso do `v-show` se você precisa mostrar/esconder algo muitas vezes, e prefira o `v-if` se a condição é pouco alternada durante a execução.
