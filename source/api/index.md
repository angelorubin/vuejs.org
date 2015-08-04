title: Visão Geral
type: api
order: 1
---

## O construtor Vue

O construtor `Vue` é o coração do Vue.js. Trata-se de uma função construtora que lhe permite criar instâncias Vue. Criar uma instância Vue é bastante simples:

``` js
var vm = new Vue({ /* opções */ })
```

Ao se criar uma instância Vue é necessário passar um objeto de configuração, que pode incluir informações sobre o elemento DOM que será observado pelo Vue, o objeto de dados, métodos mixin, escutas para eventos que são disparados durante o ciclo de vida da aplicação e mais. Veja aqui a lista completa de [opções](/api/options.html).

Cada instância Vue é essencialmente um ViewModel (por isso o nome usado para a variável `vm` que você verá na documentação). Ele contém um elemento DOM `$el` que representa a letra V no em MVVM. Também contém um objeto JavaScript `$data` que corresponde ao M. Mudanças que ocorrem em M resultam em atualizações em V. Para *two-way bindings*, um input do usuário em V resulta em mudanças em M. Para mais detalhes sobre quais são as propriedade disponível numa instância Vue, verifique [Propriedades de Instância](/api/instance-properties.html).

Cada instância Vue também contém [Métodos de Instância](/api/instance-methods.html) que estão disponíveis para observar dados, comunicação por eventos e manipulação HTML (DOM).

O construtor `Vue` expõe a [API Global](/api/global-api.html) que lhe permite estender a classe `Vue`, configurar opções globais e registrar *assets* customizados tais como componentes, diretivas, filtros entre outros.

## Inicialização

Se você informou a opção `el` durante a inicialização, a instância Vue entrará imediatamente na fase de compilação. Caso contrário ela aguardará até que `vm.$mount()` seja chamado, iniciando então a compilação. Durante a fase de compilação o Vue percorrerá todo o DOM e coletará as diretivas que encontrar, "linkando" os dados e o elemento HTML (DOM) com as diretivas. Uma vez feito o *link* estes elementos serão gerenciados pela instância Vue. Um elemento HTML (*DOM Node*) só pode ser gerenciado por uma única instância Vue e não deve ser compilado mais de uma única vez.

## Proxy de Dados

As instâncias Vue fazem proxy na hora de acessar os objetos contidos em `$data`. Assim, caso se tenha `vm.$data.msg` também se pode acessar usando simplesmente `vm.msg`. Isso pode soar um tanto quanto mágico mas é totalmente opcional. Você pode decidir por usar `vm.$data.msg` para uma abordagem mais explícita no acesso aos dados. No entanto ainda é importante mencionar a diferença entre `vm` e `vm.$data` uma vez que a primeira não pode ser observada por outras instâncias Vue para mudanças nos dados.

Também vale notar que os objetos de dados não pertencem necessariamente a uma única instância Vue - multiplos *ViewModels* podem observar o mesmo grupo de dados, seja diretamente como `$data` ou aninhados (*nested*) abaixo da instância. Isso é útil quando múltiplos componentes precisam reagir a mudança de estado de um objeto global.