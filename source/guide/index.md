title: Introdução
type: guide
order: 2
---

Vue.js (pronúncia: /vjuː/, assim como a palavra **view**) é uma biblioteca para criar interfaces web interativas. O objetivo do Vue.js é disponibilizar os benefícios do **data binding reativo** e a permitir a **composição de views utilizando componentes**, mantendo a API o mais simples possível.

O Vue.js por si mesmo não é um "framework completo" - é focado para ser exclusivamente a camada de view. Por isso que é muito simples de integrá-lo com outras bibliotecas ou projetos existentes. Por outro lado, quando utilizado com as ferramentas corretas, o Vue.js é perfeitamente capaz de criar Single-Page Applications (SPA's) sofisticadas.

Se você é um desenvolvedor frontend experiente e deseja saber mais como o Vue.js se compara a outras bibliotecas e frameworks, confira o guia de [Comparação com outros Frameworks](comparison.html); se você está mais interessado em saber como trabalhar com o Vue.js em aplicações de larga escala, confira a seção [Construindo aplicações de larga escala](application.html).

## Data Binding Reativo

No núcleo do Vue.js, o que faz com que seja extremamente simples para você manter seus dados sincronizados com o DOM é um sistema de Data Binding reativo. Quando utilizamos jQuery para manualmente manipular o DOM, o código que escrevemos é geralmente imperativo, repetitivo e propenso a erros. O Vue.js incorpora o conceito de **view orientada a dados**. Simplificando, isso significa que nós utilizamos nosso HTML comum para vincular o DOM aos dados subjacentes. Uma vez que esse vínculo é criado, o DOM manterá os dados sincronizados. Em qualquer momento que você modifique os dados, o DOM será atualizado com a informação mais recente. Como resultado disso, a maior parte da lógica de nossa aplicação é agora diretamente ligada à manipulação dos dados, ao invés de possíveis manipulações diretas do DOM. Isso faz com que nosso código seja mais fácil de ser escrito, mais simples de ser entendido e manipulado futuramente.

![MVVM](/vuejs.org/images/mvvm.png)

O exemplo mais simples possível:

``` html
<!-- essa é nossa View -->
<div id="example-1">
  Hello {{ name }}!
</div>
```

``` js
// esse é nosso Model
var exampleData = {
  name: 'Vue.js'
}

// cria uma instância do Vue, ou um "ViewModel"
// que vincula a View com o Model
var exampleVM = new Vue({
  el: '#example-1',
  data: exampleData
})
```

Resultado:
{% raw %}
<div id="example-1" class="demo">Hello {{ name }}!</div>
<script>
var exampleData = {
  name: 'Vue.js'
}
var exampleVM = new Vue({
  el: '#example-1',
  data: exampleData
})
</script>
{% endraw %}

Isso é muito similar a somente renderizar um html, mas o Vue.js fez várias coisas por trás das cortinas. Os dados e o DOM estão agora vinculados e tudo agora é **reativo**. Como nós sabemos? Simplesmente abra as ferramentas de desenvolvedor do seu navegador e modifique o valor da propriedade `exampleData.name`.Você deverá ser o exemplo acima ser atualizado conforme no valor que você digitar.

Note que nós não precisamos reescrever nenhum código de manipulação do DOM: o template HTML, melhorado com os vínculos, agora é um mapeamento declarativo dos dados, que são transformados em simples objetos Javascript. Nossa view está completamente orientada a dados.

Vamos olhar um segundo exemplo:

``` html
<div id="example-2">
  <p v-if="greeting">Hello!</p>
</div>
```

``` js
var exampleVM2 = new Vue({
  el: '#example-2',
  data: {
    greeting: true
  }
})
```

{% raw %}
<div id="example-2" class="demo">
  <span v-if="greeting">Hello!</span>
</div>
<script>
var exampleVM2 = new Vue({
  el: '#example-2',
  data: {
    greeting: true
  }
})
</script>
{% endraw %}

Aqui nós estamos visualizando algo novo. O atributo `v-if` que você está vendo é chamado de **Diretiva**. Diretivas contém o prefixo `v-` para indicar que eles são atributos especiais fornecidos pelo Vue.js, e como você deve ter advinhado, elas aplicam um comportamento reativo especial para o DOM renderizado. Teste você mesmo e atribua o valor `false` para a propriedade `exampleVM2.greeting` no seu console. Você deverá ver a mensagem "Hello!" desaparecer.

Esse segundo exemplo demonstra que não somente podemos vincular o DOM com os dados, mas nós também podemos vincular a **estrutura** do DOM aos dados. Mais que isso, o Vue.js também disponibiliza um poderoso sistema de efeitos de transição que podem aplicar efeitos efeitos automáticos ao remover ou inserir elementos com o Vue.

Existem algumas outras diretivas, cada uma com sua funcionalidade. Por exemplo, a diretiva `v-for` é utilizada para exibir itens em um Array, ou a diretiva `v-bind` é utilizada para vincular dados à atributos HTML. Nós discutiremos a sintaxe completa de data binding com mais detalhes futuramente.

## Sistema de Componentes

O Sistema de Componentes é outro importante conceito no Vue.js, porque é uma abstração que nos permite construir aplicações em larga escala focando em componentes menores, com sua própria lógica, e geralmente reutilizáveis. Quando nós pensamos sobre isso, praticamente qualquer aplicação pode ser abstraída em uma árvore de componentes:

![Component Tree](/vuejs.org/images/components.png)

Na verdade, uma típica aplicação de grande porte criada com o Vue.js pode ser representada com a figura da direita - uma árvore de componentes. Nós veremos muito mais sobre componentes mais a frente nesse guia, mas aqui está um exemplo (imaginário) do que um template HTML se pareceria utilizando componentes:

``` html
<div id="app">
  <app-nav></app-nav>
  <app-view>
    <app-sidebar></app-sidebar>
    <app-content></app-content>
  </app-view>
</div>
```

Você deve ter percebido que os componenets do Vue.js são muito parecidos com **Elementos Personalizados**, que são parte da [Especificação para Componentes Web](http://www.w3.org/wiki/WebComponents/). Na verdade, a sintaxe de componentes do Vue.js são inspirados em parte na especificação. Por exemplo, os componentes Vue implementam a [Slot API](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md) e o atributo especial `is`. Entretanto, existem algumas diferenças:

1. A Especificação de Componentes Web ainda está em progresso, e não está implementada nativamente em todos os navegadores. Como comparação, os componentes Vue não precisam de nenhum polyfill e funcionam consistentemente em todos os navegadores suportados (IE 9 e superiores). Quando preciso, os componentes do Vue.js podem ser incluídos dentro de um "elemento personalizado nativo".

2. Componentes Vue.js fornecem importantes funcionalidades que não estão disponíveis em elementos personalizados simples, mais notado pelo fluxo de dados entre componentes, compartilhamento de informações de eventos e troca de componentes utilizando efeitos de transição.

O sistema de componentes é a base para criar aplicativos complexos com o Vue.js. Em adição à esse sistema, o ecossistema do Vue.js fornece várias ferramentas (simples e avançadas) que podem ser combinados e criar um ambiente mais parecido com o de um framework.
