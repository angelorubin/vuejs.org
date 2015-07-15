title: Instalação
type: guide
order: 1
vue_version: 0.12.6
dev_size: 219.64
min_size: 67.73
gz_size: 22.00
---

> **Aviso de Compatibilidade:** Vue.js não suporta IE8 ou anterior.

## Download Direto

Simplesmente efetue o download da biblioteca e a inclua utilizando uma tag script. O `Vue` será registrado como uma variável global.

<div id="downloads">
<a class="button" href="https://raw.github.com/yyx990803/vue/{{vue_version}}/dist/vue.js" download>Versão para Desenvolvimento</a><span class="light info">{{dev_size}}kb, com vários comentários para debug e mensagens sobre erros.</span>

<a class="button" href="https://raw.github.com/yyx990803/vue/{{vue_version}}/dist/vue.min.js" download>Versão para Produção</a><span class="light info">{{min_size}}kb minificado / {{gz_size}}kb gzipped</span>
</div>

Também disponível em [jsdelivr](//cdn.jsdelivr.net/vue/{{vue_version}}/vue.min.js) ou [cdnjs](//cdnjs.cloudflare.com/ajax/libs/vue/{{vue_version}}/vue.min.js) (pode demorar algum tempo para sincronizar com os lançamentos, então a versão mais atual pode não estar disponível ainda).

## NPM

``` bash
$ npm install vue
# para a versão de desenvolvimento:
$ npm install yyx990803/vue#dev
```

## Bower

``` bash
# somente a versão estável está disponível no Bower
$ bower install vue
```

## Duo

```js
var Vue = require('yyx990803/vue')
// para a versão de desenvolvimento:
var Vue = require('yyx990803/vue@dev')
```

## Módulos AMD (AMD Module Loader)

As versões offline (standalone) ou instaladas via Bower são carregadas via UMD, tornando possível utilizá-las diretamente como um módulo AMD

## Pronto?

[Comece aqui!](/guide/)
