title: Propriedades da Instância
type: api
order: 3
---

### vm.$el

- **Tipo:** `HTMLElement`
- **Somente leitura**

O elemento HTML que a instância Vue está gerenciando. Note que para [Instâncias Fragmentadas](/guide/best-practices.html#Fragment_Instance) `vm.$el` retornará uma elemento âncora (`<a>`) que indica a posição inicial do fragmento.

### vm.$data

- **Tipo:** `Objeto`

O objeto de dados que a instância Vue está observando. Você pode troca-lo por um novo objeto. A instância Vue faz proxy de acesso aos dados deste objeto.

### vm.$options

- **Tipo:** `Objeto`

São opções customizadas passadas na instanciação do objeto Vue. Mostram-se úteis quando se precisa adicionar suas próprias informações à instância Vue:

``` js
new Vue({
  customOption: 'foo',
  created: function () {
    console.log(this.$options.customOption) // -> 'foo'
  }
})
```

### vm.$parent

- **Type:** `Vue`
- **Somente leitura**

É a instância pai da instância atuao, caso esta possua uma.

### vm.$root

- **Type:** `Vue`
- **Somente leitura**

É a instância Vue raiz da árvore de componentes. Se a instância atual não possui qualquer pai este valor será a própria instância.

### vm.$children

- **Type:** `Array<Vue>`
- **Somente leitura**

Os componentes filhos diretos da instância atual.

### vm.$

- **Tipo:** `Objeto`
- **Somente leitura**

Um objeto que guarda os componentes filhos que usam `v-ref`. Para mais detalhes veja [v-ref](/api/directives.html#v-ref).

### vm.$$

- **Tipo:** `Objeto`
- **Somente leitura**

Um objeto que guarda elementos HTML que usam `v-el`. Para mais detalhes veja [v-el](/api/directives.html#v-el).

### Propriedades Meta

Instâncias criadas pela diretiva `v-repeat` também conterão propriedades meta. Exemplo: `vm.$index`, `vm.$key` e `vm.$value`. Para mais detalhes veja [o guia de uso de `v-repeat`](/guide/list.html).
