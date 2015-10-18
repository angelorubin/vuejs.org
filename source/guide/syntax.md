title: Sintaxe de Data Binding
type: guide
order: 4
---

O Vue.js utiliza uma implementação de DOM baseado em templates. Isso significa que todos os templates Vue.js são essencialmente um código HTML válido e melhorado com alguns atributos especiais. Mantenha isso em mente, já que isso é o que torna os templates Vue diferentes de templates baseados em string.

## Interpolações

### Texto

A forma mais básica de data binding é uma interpolação com texto, utilizando a sintaxe Mustache (Duas chaves):

``` html
<span>Message: {{ msg }}</span>
```

A tag mustache será substituída com o valor da propriedade `msg` no objeto de dados correspondente. O texto também será automaticamente atualizado em qualquer momento que o valor da propriedade `msg` seja modificado.

Você também pode criar interpolações que ocorram somente uma vez:

``` html
<span>This will never change: {{* msg }}</span>
```

### HTML Puro

As duas chaves interpretam os dados como um texto simples, não HTML. Para exibir um código HTML, você precisará utilizar três chaves.

``` html
<div>{{{ raw_html }}}</div>
```

Os conteúdos são exibidos como HTML simples - qualquer data binding será ignorado. Se você precisa reutilizar partes de templates, você deve utilizar [templates parciais](/api/#partial).

<p class="tip">Renderização de HTML arbitrário no seu site pode ser muito perigoso, porque ele pode levar à um [Ataque XSS](https://en.wikipedia.org/wiki/Cross-site_scripting). Somente utilize a interpolação HTML em conteúdos que você confia e **nunca** em conteúdos fornecidos por usuários.</p>

### Atributos

Chaves também podem ser utilizados dentro de atributos HTML:

``` html
<div id="item-{{ id }}"></div>
```

Entretanto, esse recurso somente pode ser utilizado dentro de atributos HTML nativos. Você não pode utilizar chaves em atributos personalizados ou em diretivas do Vue.js. Não se preocupe, o Vue.js emitirá alertas para você quando as chaves forem utilizadas em locais errados.

## Binding Expressions

O texto que nós colocamos dentro das chaves são chamados **binding expressions**. No Vue.js, uma binding expression (expressão de ligação) consiste em uma única JavaScript expression que pode ser seguida de um ou mais filtros.

### JavaScript Expressions

Até agora nós somente vinculamos simples propriedades chave em nossos templates. Mas, na verdade, o Vue.js suporta todo o poder das JavaScript Expressions dentro de data bindings.

``` html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}
```

Essas expressões serão processadas no escopo dos dados do proprietário da instância Vue. Uma restrição é que cada vínculo pode conter somente uma **única expressão**, então os exemplos a seguir **NÃO** funcionarão:

``` html
<!-- isso é uma afirmação, não uma expressão: -->
{{ var a = 1 }}

<!-- Controle de fluxo também não funcionará, use expressões ternárias -->
{{ if (ok) { return message } }}
```

### Filtros

O Vue.js permite que você opcionalmente anexe "filtros" no fim de uma expressão, utilizando o símbolo "pipe":

``` html
{{ message | capitalize }}
```

Aqui nós estamos "passando" o valor da expressão `message` pelo filtro `capitalize`, que já vem com o Vue.js. Esse filtro, na verdade, é somente uma função que retorna o valor com a primeira letra em maiúsculo. O Vue.js fornece alguns filtros por padrão, e nós discutiremos como você pode escrever o seu próprio filtro no futuro.

Note que a sintaxe utilizando o pipe não é parte da sintaxe Javascript, portanto você não pode misturar os filtros dentro de expressões; você somente pode anexá-los no fim de uma expressão.

Filtros podem ser encadeados:

``` html
{{ message | filterA | filterB }}
```

Filtros podem receber parâmetros:

``` html
{{ message | filterA 'arg1' arg2 }}
```

A função de filtro sempre recebe o valor da expressão como primeiro parâmetro. Parâmetros dentro de aspas são interpretados como texto simples, enquanto os que não tem aspas são interpretados como expressões. Aqui, o texto simples `"arg1"` será passado para o filtro como segundo argumento, e o valor da expressão `arg2` será processado e passado como terceiro parâmetro.

## Diretivas

Diretivas são atributos especiais que recebem o prefixo `v-`. Espera-se que o valor dos atributos de uma diretiva sejam **binding expressions**, então as regras sobre as JavaScript expressions e os filtros que mencionamos acima valem aqui também. O trabalho de uma diretiva é de aplicar um comportamento especial ao DOM reativamente, quando o valor da sua expressão for modificado. Vamos revisar o exemplo que vimos na introdução:

``` html
<p v-if="greeting">Hello!</p>
```

Aqui, a diretiva `v-if` irá remover/inserir o elemento `<p>` baseado na veracidade do valor da expressão `greeting`.

### Parâmetros

Algumas diretivas podem receber parâmetros, denotados por dois pontos após o nome da diretiva. Por exemplo, a diretiva `v-bind` é utilizada para alterar o valor de um atributo HTML reativamente:

``` html
<a v-bind:href="url"></a>
```

Aqui o parâmetro é o `href`, que aponta para a diretiva `v-bind` que o valor do `href`do elemento deve ser igual ao valor da expressão `url`. Você deve ter percebido que isso é o mesmo que escrever o seguinte código utilizando interpolação: `{% raw %}href="{{url}}"{% endraw %}`: isso é verdade, e, na verdade, a interpolação de atributos são traduzidas para a diretiva `v-bind` internamente.

Outro exemplo é a diretiva `v-on`, que é responsável por observar os eventos do DOM:

``` html
<a v-on:click="doSomething">
```

Aqui o parâmetro é o nome do evento que deve ser observado. Nós discutiremos mais sobre como lidar com os eventos em mais detalhes em breve.

### Modificadores

Modificadores são sufixos especiais representados por um ponto, que indica que aquela diretiva deve ser construída com um comportamento especial. Por exemplo, o modificador `.literal` indica que aquela diretiva deve utilizar o parâmetro como um texto simples, ao invés de uma expressão. Veja o exemplo:

``` html
<a v-bind:href.literal="/a/b/c"></a>
```

Claro, isso parece não fazer sentido porque nós poderíamos somente escrever `href="/a/b/c"` ao invés de utilizar uma diretiva. O exemplo acima é só para demonstrar a sintaxe, e você vai ver mais cenários práticos para esses modificadores ao longo da documentação.

## Abreviações

O prefixo `v-if` serve como uma sugestão visual para identificar atributos específicos do Vue.js em seus templates. Isso é útil quando você está aplicando comportamentos dinâmicos para um markup existente, mas pode ser um pouco verboso para diretivas que você utiliza frequentemente. Ao mesmo tempo, o uso do prefixo `v-` se torna menos importante se você estiver construindo uma SPA onde o Vue.js gerencia todo seu template. Portanto, o Vue.js fornece algumas abreviações especiais para duas das mais utilizadas diretivas, `v-bind` e `v-on`:

### Abreviação `v-bind`

``` html
<!-- sintaxe completa -->
<a v-bind:href="url"></a>

<!-- abreviação -->
<a :href="url"></a>
```

### Abreviação `v-on`

``` html
<!-- sintaxe completa -->
<a v-on:click="doSomething"></a>

<!-- abreviação -->
<a @click="doSomething"></a>
```

Eles podem parecer um pouco diferente de um HTML "válido", mas todos os navegadores que o Vue.js suporta convertem eles corretamente, e eles não aparecem no HTML após serem renderizados. Essa sintaxe com abreviação é completamente opcional, e você deve começar a gostar mais dela quando você aprender mais sobre seu uso no futuro.
