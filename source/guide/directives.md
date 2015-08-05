title: Diretivas
type: guide
order: 3
---

## Resumo

Se você não utilizou AngularJS anteriormente, então provavelmente não sabe o que é uma diretiva. Essencialmente, uma diretiva é representada por um token especial em um elemento html, e isso fará com que a biblioteca faça algo com esse elemento. Com o Vue.js, esse conceito é drasticamente mais simples do que no Angular. Uma diretiva do Vue.js somente aparecerá no formato de um atributo HTML com um prefixo, que se parecerá com o código a seguir:

``` html
<element
  prefix-directiveId="[argument:] expression [| filters...]">
</element>
```

## Um simples exemplo

``` html
<div v-text="mensagem"></div>
```

Aqui o prefixo é o `v`, que vem como padrão. O ID dessa diretiva é o `text`, e a expressão é a `mensagem`. Essa diretiva diz para o Vue.js atualizar o atributo `textContent` da div sempre que a propriedade `mensagem` da instância do Vue for alterada.

## Expressões em Linha

``` html
<div v-text="'Olá ' + usuario.nome + ' ' + usuario.sobrenome"></div>
```

Aqui nós estamos utilizando uma *expressão computada* ao invés de uma simples propriedade. O Vue.js irá rastrear automaticamente as propriedades envolvidas na expressão, e sempre que uma delas for atualizada o valor será atualizado também na expressão. Graças às atualizações assíncronas, mesmo quando múltiplas dependências mudarem, as atualizações serão realizadas uma única vez durante o loop do evento.

Você deve usar expressões com cuidado, e evitar incluir muita lógica nos templates, especialmente expressões com efeitos colaterais (com excessão de expressões para observar eventos). Para desencorajar o excesso de complexidade, as expressões em linha permitem apenas o uso de **uma declaração**. Para *bindings* que precisam de operações mais complicadas, é recomendado o uso de [Propriedades Computadas](/guide/computed.html).

<p class="tip">Por razões de segurança, em expressões em linha você só pode acessar propriedades e métodos presentes no contexto atual da instância do Vue e de seus pais.</p>

## Argumento

``` html
<div v-on="click : clickHandler"></div>
```

Algumas diretivas requerem um argumento antes de realizar uma ação. Nesse exemplo, o argumento `click` indica que nós queremos utilizar a diretiva `v-on` para observar um evento de click e então chamar o método `clickHandler` da instância do *ViewModel*.

## Filtros

Filtros podem ser anexados às diretivas ou expressões para processarem os valores antes de atualizar o DOM. Filtros são denotados por um pipe simples (`|`) assim como em shell scripts. Para mais detalhes confira [Tudo sobre Filtros](/guide/filters.html).

## Várias cláusulas

Você pode adicionar múltiplas parâmetros em uma mesma diretiva de um único atributo, separando com vírgulas. O Vue se encarregará de transformá-los em múltiplas instâncias dessa diretiva.

``` html
<div v-on="
  click   : onClick,
  keyup   : onKeyup,
  keydown : onKeydown
">
</div>
```

## Diretivas Literais

Algumas diretivas não criam *data bindings* - elas simplesmente pegam o valor do atributo como uma string. Por exemplo, veja a diretiva`v-ref`:

``` html
<my-component v-ref="alguma-string"></my-component>
```

Aqui, `"alguma-string"` não é uma expressão reativa - então o Vue.js não vai tentar encontrá-la nas propriedades do componente.

Em alguns casos você também pode utilizar a sintaxe *mustache* para deixar uma diretiva literal reativa:

``` html
<div v-show="exibirMsg" v-transition="{{idTransicaoDinamica}}"></div>
```

Entretanto, note que a diretiva `v-transition` é a única que possui essa característica. Expressões com a sintaxe *mustache* em outras diretivas literais, como a `v-ref` e a `v-el`, terão seu valor processado **somente uma vez**. Depois que a diretiva for compilada, qualquer mudança nesses atributos não causarão mudança no HTML.

Uma lista completa das diretivas literais pode ser encontrada na [API Completa](/api/directives.html#Literal_Directives).

## Diretivas Vazias

Algumas diretivas nem ao mesmo esperam que um valor seja passado como parâmetro - elas simplesmente realizam alguma ação no elemento uma única vez. Por exemplo, a diretiva `v-pre`:

``` html
<div v-pre>
  <!-- nenhum código markup que for inserido aqui será compilado. -->
</div>
```

Uma lista completa das diretivas vazias pode ser encontrado na [API Completa](/api/directives.html#Empty_Directives).

Agora, vamos falar sobre [Filtros](/guide/filters.html).
