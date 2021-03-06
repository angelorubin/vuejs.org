title: Instalação
type: guide
order: 1
vue_version: 1.0.0-beta.4
dev_size: "237.54"
min_size: "71.68"
gz_size: "23.55"
---

> **Nota de Compatibilidade:** O Vue.js não suporta IE 8 e inferiores.

## Download Direto

Simplesmente efetue o download da biblioteca e a inclua utilizando uma tag script. O `Vue` será registrado como uma variável global.

<div id="downloads">
<a class="button" href="https://raw.github.com/yyx990803/vue/{{vue_version}}/dist/vue.js" download>Versão de Desenvolvimento</a><span class="light info">{{dev_size}}kb, com vários comentários para debug e mensagens sobre erros.</span>

<a class="button" href="https://raw.github.com/yyx990803/vue/{{vue_version}}/dist/vue.min.js" download>Versão para Produção</a><span class="light info">{{min_size}}kb minificado / {{gz_size}}kb gzipped</span>
</div>

### CDN

Disponível em [jsdelivr](//cdn.jsdelivr.net/vue/{{vue_version}}/vue.min.js) or [cdnjs](//cdnjs.cloudflare.com/ajax/libs/vue/{{vue_version}}/vue.min.js) (pode demorar algum tempo para sincronizar com os lançamentos, então a versão mais atual pode não estar disponível ainda).

### CSP-compliant build

Alguns ambientes, como os Aplicativos do Google Chrome, obrigam o uso de Políticas de Segurança de Conteúdo (CSP) e não permitem o uso do `new Function()` ao criar expressões. Nesses casos você deve utilizar a build [CSP-compliant build](https://github.com/yyx990803/vue/tree/csp/dist).

## NPM

``` bash
$ npm install vue
# para a versão csp-compliant:
$ npm install vue@csp
# para a build de desenvolvimento (direto do GitHub):
$ npm install yyx990803/vue#dev
```

## Bower

``` bash
# somente a versão estável está disponível no Bower
$ bower install vue
```

## Módulos AMD (AMD Module Loader)

As versões offline (standalone) ou instaladas via Bower são carregadas via UMD, tornando possível utilizá-las diretamente como um módulo AMD
