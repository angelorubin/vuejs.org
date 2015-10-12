title: Classes e Vinculação de Estilos
type: guide
order: 6
---

Um requisito comum para vinculação de dados é a manipulação dos estilos que envolvem o elemento. Já que eles são atributos, nós podemos utilizar a diretiva `v-bind` para lidar com isso: nós somente precisamos calcular o texto final com nossas expressões. Entretanto, lidar com concatenação de textos geralmente é chato e propenso a erros. Por essa razão, o Vue.js fornece melhorias especiais quando o `v-bind` é utilizado para `class`e `style`. Em adição às strings, as expressões podem também ser processadas como objetos e arrays.

## Vinculando Classes HTML

### Sintaxe de Objeto

Nós podemos passar um objeto para a diretiva `v-bind:class` para trocar as classes dinamicamente:

``` html
<div v-bind:class="{ 'class-a': isA, 'class-b': isB }"></div>
```
``` js
data: {
  isA: true,
  isB: false
}
```

Que irá renderizar:

``` html
<div class="class-a"></div>
```

Quando `isA` e `isB` são modificados, a lista de classes será modificada de acordo. Por exemplo, se `isB` se tornar `true`, a lista de classes irá ficar assim: `"class-a class-b"`.

Você também pode vincular diretamente à objeto nos dados do Vue:

``` html
<div v-bind:class="classObject"></div>
```
``` js
data: {
  classObject: {
    'class-a': true,
    'class-b': false
  }
}
```

Isso vai renderizar o mesmo resultado. Como você deve ter percebido, você também pode vincular a uma [propriedade computada](computed.html) que retorna um objeto. Esse é um padrão comum e muito poderoso.

### Sintaxe de Array

Nós podemos passar um array para a diretiva `v-bind:class` para aplicar uma lista de classes:

``` html
<div v-bind:class="[classA, classB]">
```
``` js
data: {
  classA: 'class-a',
  classB: 'class-b'
}
```

Renderizará:

``` html
<div class="class-a class-b"></div>
```

Caso você queira adicionar uma classe à lista condicionalmente, você pode utilizar uma expressão ternária:

``` html
<div v-bind:class="[classA, isB ? classB : '']">
```

Esse código sempre aplicará a classe `classA`, mas somente aplicará a classe `classB` quando a propriedade `isB` for `true`.

## Encadeando Estilos

### Sintaxe de Objeto

A sintaxe de objeto para `v-bind:style` é bem simples - quase se parece com CSS, mas na verdade é um objeto JavaScript. Voc~e pode utilizar tanto <i>camelCase</i> ou <i>kebab-case</i> para o nome das propriedades CSS:

``` html
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```
``` js
data: {
  activeColor: 'red',
  fontSize: 30
}
```

Geralmente é uma boa ideia estilizar o objeto diretamente, deixando assim o template mais limpo:

``` html
<div v-bind:style="styleObject"></div>
```
``` js
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

Novamente, a sintaxt de objeto é geralmente utilizada em conjunto com propriedades computadas que retornam objetos.

### Sintaxe de Array

A sintaxe de array para a diretiva `v-bind:style` permite que você aplique múltiplos objetos de estilo para o mesmo elemento:

``` html
<div v-bind:style="[styleObjectA, styleObjectB]">
```

### Utilizando <i>Auto-prefixing</i>

Quando você usa uma propriedade CSS que requere prefixos de "fornecedores" (<i>vendor</i>) na diretiva `v-bind:style`, por exemplo, `transform`, o Vue.js irá detectar automaticamente e adicionar os prefixos necessários para os estilos aplicados.
