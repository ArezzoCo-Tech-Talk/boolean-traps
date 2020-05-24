# Boolean traps & Object literal function parameters

Tech Talk para falar um pouco mais sobre boolean traps no JavaScript e como evitá-los utilizando o conceito de object literal function parameters

## Oque são boolean traps?

Boolean traps ocorrem quando uma função recebe um parâmetro do tipo boolean e não sabemos exatamente para oque aquele booleano serve e oque ele irá fazer dentro da função. Podemos chamar isso também de function parameter traps.

```js
NavigationService.popToTop(true)
```

Oque o true faz? Chutem.

Perguntas que vão surgir ao ver essa chamada em algum lugar no código:

- Oque `true` significa nesse contexto?
- Oque ele faz dentro do código?
- E se eu não passar nada?
- E se eu passar false?

Todas essas perguntas são difíceis de serem respondidas em um primeiro momento, portanto teriamos que analisar a implementação dessa função, ou, no caso de uma lib ler a documentação e então descobrir oque o parâmetro significa e faz.

----

Vamos para mais um exempo:

Nesse caso temos uma função com 6 parâmetros, dentre eles `booleanos`, `arrays` e `strings`, porém, ao bater o olho nessa chamada, eu não consigo saber com clareza oque cada um desses parâmetros faz, teria que olhar na documentação. Novamente me surgem todas aquelas duvidas. 

- Oque o parâmetro significa?
- Oque ele faz dentro do código?
- E se eu não passar nada?

```js
getHeaderOptions(true, true, [], [], false, "Batata")
```

Vamos piorar mais ainda a situação, digamos que alguns desses parâmetros sejam opcionais. Na hora de declarar a função eu teria que cuidar para que os parâmetros opcionais ficassem por último, já que, para passar o terceiro parâmetro eu preciso obrigatoriamente passar o primeiro e o segundo.

E se todos os parâmetros forem opcionais e eu quiser passar apenas o parâmetro `"Batata"` a chamada vai continuar igual:

```js
getHeaderOptions(true, true, [], [], false, "Batata")
```

----

E se eu quiser adicionar um novo parâmetro a essa função? 

1) Eu adiciono esse parametro como último parâmetro, assim, todos os lugares que chamam a função, continuam funcionando, sem precisar de refactor, mas, se alguns dias depois eu precisar adicionar mais um parâmetro, o refactor é obrigatório.

2) Adiciono ele no meio ou no final dos parâmetros e refatoro todos os lugares que a função está sendo usada.

----

Podemos ver essas falhas até mesmo em funções nativas do JavaScript, como é o caso da função `replace`.

```js
'string de teste'.replace(' ', '-')
```

Estou fazendo replace do `' '` por um `-` ou o contrário? Talvez para quem usa o JS a tempo seja claro, mas para muitos isso pode parecer confuso e faz com que essas pessoas tenham que visitar a documentação diversas vez.

----

Enfim, vimos varios exemplos de boolean traps e como eles afetam o desenvolvimento e o entendimento do código de uma aplicação. Eles afetam coisas como:
- Legibilidade do código
- Dificuldade para utilização das funções
- Dificuldade para modificação das funções
- Dificuldade no entendimento das funções

Como podemos evitar isso e melhorar todos esses pontos citados acima?

## OBJECT LITERAL FUNCTION PARAMETERS

Esse conceito baseia se em sempre passar um `objeto` como parâmetro de uma função e ja fazer a desestruturação dele diretamente na declaração da função.
Vamos voltar para o primeiro exemplo que foi passado para entender melhor:

**Declaração**

Antes:
```js
NavigationService.popToTop(immediate) {
  this.navigator.dispatch(StackActions.popToTop({ immediate }))
}
```

Depois:
```js
NavigationService.popToTop({ immediate }) {
  this.navigator.dispatch(StackActions.popToTop({ immediate }))
}
```

**Chamada**

Antes:
```js
NavigationService.popToTop(true)
```

Depois:
```js
NavigationService.popToTop({ immediate: true })
```
  
Como podemos ver, a declaração da função nesse caso não mudou muito, apenas na chamada que conseguimos ver o real ganho, fica muito mais facil de entender oque aquele parâmetro faz. Immediate remete a algo imediato, portanto consigo entender que passando `true` aquela função será executada imediatamente sem animações ou delays.
  
----

E quanto ao `getHeaderOptions` com aquela quantidade enorme de parâmetros que não se sabe exatamente para oque servem?

Antes:
```js
getHeaderOptions(true, true, [], [], false, "Batata")
```
Depois: 
```js
getHeaderOptions({
    isTransparent: true,
    isVisible: true,
    rightButtons: [],
    leftButtons: [],
    showBackArrow: false,
    headerTitle: 'Batata',
})
```

Agora fica muito mais claro entender oque cada um dos parâmetros significa e não é necessário acessar nenhuma documentação ou a declaração da função para entedê-la.
 
Utilizando o conceito de object literal function parâmetes é possível fazer com que qualquer um dos parâmetros seja opcional e alem disso, a ordem com que esses parâmetros é passada para a função é indifere ja que ela se baseia na chave utilizada dentro do objeto.
 
Se todos forem opcionais e eu quiser passar apenas o parâmetro `"Batata"`:

```js
getHeaderOptions({ headerTitle: 'Batata' })
```
E se eu quiser adicionar um novo parâmetro a essa função? 

Bom, basta adicionar uma nova chave ao objeto que está sendo desestruturado na função e passar um valor default para ele que todos os lugares em que a função está sendo utilizada irão continuar funcionando, sem muitos refactors e problemas de retrocompatibilidade de código.

A função que antes era declarada assim:
```js
getHeaderOptions({
    isTransparent = true,
    isVisible = true,
    rightButtons = [],
    leftButtons = [],
    showBackArrow = true,
    headerTitle,
  } = {}) {...}
```
Ira continuar assim, porém com uma nova chave:

```js
getHeaderOptions({
    isTransparent = true,
    isVisible = true,
    rightButtons = [],
    leftButtons = [],
    showBackArrow = true,
    novaChave = 'valor default',
    headerTitle,
  } = {}) {...}
```

E todos os lugares que estavam utilizando ela sem passar esse valor poderão continuar não passando esse valor que o valor default será utilizado.

----

Já o caso da função `replace` caso ela seguisse esse padrão, poderiamos chamar algo como:

```js
'string teste'.replace({oldCharacter: ' ', newCharacter: '-'})
```

Muito mais claro e de fácil entendimento, mas como iremos ver mais pra frente mais verboso.

## Vantagens dos Object Literal Funtion Parameters

- Código mais legível
- Valores default em qualquer um dos parametros
- Maior facilidade no uso da função
- Não precisamos nos preocupar com a ordem que os parâmetros são passados
- Maior escalabilidade e possibilidade de refactors

## Desvantagens 

- Código mais verboso
- Um pouco mais complexo de ser entendido por iniciantes

## Referências

Lea Verou - JS UX: Writing code for humans - BrazilJS Conf 2016: https://www.youtube.com/watch?v=loj3CLHovt0&list=PLIjRSZIgC41N7neVqbcKVaS0A4TxY_aLL&index=8
