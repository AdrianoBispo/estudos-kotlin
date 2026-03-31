# 04 - Funções (Kotlin vs TypeScript)

No JavaScript e TypeScript, as funções são o coração da linguagem: são "cidadãos de primeira classe", o que significa que podem ser armazenadas em variáveis, passadas como argumentos para outras funções e retornadas como resultado.

No Kotlin, a história é rigorosamente a mesma. A linguagem suporta totalmente o paradigma de programação funcional e combina-o com a Orientação a Objetos de forma magistral. A grande diferença aqui é a sintaxe incrivelmente enxuta do Kotlin (especialmente no trato com Lambdas) e a introdução de superpoderes nativos, como as **Funções de Extensão** e as **Funções com Receptor**.

Compreender este módulo a fundo é o requisito número um para usar o Jetpack Compose, pois o Compose não usa classes para desenhar a interface (como o Angular ou os Class Components antigos do React), mas sim **árvores de funções**.

## 1. Funções Padrão, `Unit` e Única Expressão

No TypeScript, usamos a palavra-chave function ou arrow functions. No Kotlin, usamos a palavra-chave fun.

### O Tipo `Unit` (O adeus ao `void` e `undefined`)

No JavaScript, uma função que não retorna nada devolve implicitamente `undefined`. No TypeScript, tipamos isso como `void`. No Kotlin, o tipo de retorno para funções sem resultado é o `Unit`. A diferença é que `Unit` é um objeto real (um Singleton), o que permite que ele seja usado em programação genérica de forma muito mais coesa que o `void` do Java/TS.

#### TypeScript:

``` TS
function saudar(nome: string): void {
    console.log(`Olá, ${nome}`);
}

function somar(a: number, b: number): number {
    return a + b;
}
```

#### Kotlin:

``` kotlin
// Função Nomeada Padrão. O ': Unit' é opcional, o compilador infere.
fun saudar(nome: String) {
    println("Olá, $nome")
}

fun somar(a: Int, b: Int): Int {
    return a + b
}
```

### Funções de Única Expressão (Single-Expression Functions)

Quando uma função consiste em apenas uma linha que retorna um valor, podemos omitir as chaves `{}` e a palavra `return`, usando o sinal de igual `=`. É o equivalente direto aos retornos implícitos das **arrow functions** no JS. E o mais poderoso: podemos combinar isso com expressões como `if` e `when`. Veja o exemplo abaixo:

``` kotlin
// Sintaxe enxuta máxima. O tipo de retorno (Int) é inferido.
fun somarRapido(a: Int, b: Int) = a + b

// Combinando Single-Expression com 'when'
fun classificarIdade(idade: Int) = when {
    idade < 18 -> "Menor de idade"
    idade in 18..59 -> "Adulto"
    else -> "Sênior"
}
```

## 2. Parâmetros Nomeados e Valores por Defeito

No TypeScript, quando uma função tem muitos parâmetros ou parâmetros opcionais, o padrão adotado pela comunidade é passar um objeto de opções e desestruturá-lo (`function criarBotao({texto, cor = "azul", arredondado = true})`).

No Kotlin, isso não é necessário. A linguagem suporta nativamente **Default Arguments** (valores por padrão) e **Named Arguments** (argumentos nomeados), eliminando a necessidade de objetos complexos de configuração ou do verboso **Builder Pattern** do Java. Observe o exemplo:

``` kotlin
// Função com valores por padrão predefinidos
fun criarBotao(texto: String, cor: String = "Azul", arredondado: Boolean = true, onClick: () -> Unit) {
    println("Botão '$texto' criado. Cor: $cor, Arredondado: $arredondado")
}

// 1. Chamada posicional normal (Temos de respeitar a ordem)
criarBotao("Enviar", "Vermelho", false, {})

// 2. Chamada com Parâmetros Nomeados (Ignoramos a ordem e os valores padrão!)
criarBotao(
    onClick = { println("A processar envio...") },
    texto = "Submeter"
    // 'cor' será "Azul" e 'arredondado' será true automaticamente.
)
```

> **No Jetpack Compose:** Esta funcionalidade é usada exaustivamente. Um componente `TextField` (input de texto) tem quase 20 parâmetros possíveis, mas você só passas o `value`, `onValueChange` e o `modifier`, ignorando os outros 17. 

## 3. Expressões Lambda (As Arrow Functions do Kotlin)

As lambdas no Kotlin são blocos de código que podem ser passados como valores. São o equivalente direto às _arrow functions_ do JS `() => {}`, mas a sintaxe usa apenas chaves `{}` e o sinal `->` fica dentro das chaves.

#### TypeScript:

``` TS
const dobro = (x: number) => {
    return x * 2;
};
```

#### Kotlin:

``` kotlin
// A estrutura é: { parametros -> corpo_da_funcao }
val dobro = { x: Int -> x * 2 }

println(dobro(5)) // Imprime 10
```

### O Retorno Implícito das Lambdas

Uma diferença crucial: dentro de uma lambda no Kotlin, **não se usa a palavra** `return` (usá-la causaria um _non-local return_, saindo da função exterior que a chamou).Em vez disso, **a última expressão/linha da lambda é automaticamente o seu valor de retorno**.

```kotlin

val calcularComplexo = { x: Int, y: Int ->
    val soma = x + y
    val multiplicador = 2
    soma * multiplicador // Esta última linha é o que a lambda vai retornar!
}
```

### O Superpoder do `it` (Parâmetro Implícito)

Se a sua lambda tiver apenas um parâmetro, não precisas de o declarar! O Kotlin chama-o automaticamente de `it` (ele). É ótimo para códigos curtos e limpos.

``` kotlin
val lista = listOf(1, 2, 3, 4, 5)

// Em vez de escrever: { numero -> numero * 2 }
val dobrados = lista.map { it * 2 }

// Em vez de: { numero -> numero % 2 == 0 }
val pares = lista.filter { it % 2 == 0 }
```

## 4. Funções de Ordem Superior e Trailing Lambdas

Funções de Ordem Superior (Higher-Order Functions) são funções que recebem outras funções como parâmetros ou as retornam. É o pilar da programação reativa e funcional.

``` kotlin
// Esta função recebe dois números e uma ação (uma lambda) para executar sobre eles.
fun operarNumeros(a: Int, b: Int, operacao: (Int, Int) -> Int): Int {
    return operacao(a, b)
}
```

### ⚠️ A Sintaxe de "Trailing Lambda" (A regra de ouro do Compose)

Esta é a regra sintática **mais importante** que deve aprender para o Jetpack Compose.A linguagem Kotlin possui uma convenção: **Se o último parâmetro de uma função for uma função (uma lambda), você pode fechar os parênteses `()` da chamada e abrir as chaves `{}` fora deles.**

``` kotlin
// Chamada normal (funciona, mas é considerada feia no Kotlin):
operarNumeros(10, 5, { x, y -> x + y })

// Chamada com Trailing Lambda (Elegante, idiomática e padrão na linguagem):
operarNumeros(10, 5) { x, y -> 
    x + y 
}
```

#### Por que é que isso existe? Para criar DSLs (Domain Specific Languages).

Essa sintaxe faz com que funções se pareçam com blocos nativos da linguagem (como `if` ou `for`). É exatamente assim que o Jetpack Compose constrói interfaces declarativas, parecendo uma estrutura de árvore semelhante ao `HTML/JSX`:

``` kotlin
// Exemplo real de Jetpack Compose:
Row(
    modifier = Modifier.fillMaxWidth(),
    horizontalArrangement = Arrangement.SpaceBetween
) { 
    // Isto aqui dentro é o ÚLTIMO parâmetro do 'Row', que é uma lambda chamada 'content'.
    Text(text = "Esquerda")
    Button(onClick = { /* Lógica de clique */ }) {
        Text("Direita")
    }
}
```

## 5. Funções de Extensão (Extension Functions e Properties)

Este é um dos recursos mais amados do Kotlin. Permite "injetar" novas funções em classes existentes que você não controlas (como `String`, `Int`, `List`, ou classes de bibliotecas do Android), **sem ter de herdar dessas classes.**

No JavaScript, tentar fazer isso (modificando o `String.prototype`) é considerado um anti-padrão perigoso ("Prototype Pollution") porque altera a classe globalmente e pode causar conflitos de nomes em tempo de execução. No Kotlin, é 100% seguro: a extensão é resolvida **estaticamente** pelo compilador no momento da importação.

``` kotlin
// Adicionando um validador de emails diretamente à classe String do Kotlin
fun String.isEmailValido(): Boolean {
    // A palavra 'this' refere-se à instância da String que está a chamar a função
    return this.contains("@") && this.endsWith(".com")
}

val meuEmail = "teste@exemplo.com"
// Fica a parecer um método nativo da linguagem!
println(meuEmail.isEmailValido()) // Imprime: true
```

- **Propriedades de Extensão (Extension Properties):** Além de funções, pode criar propriedades falsas que calculam valores dinamicamente.

``` kotlin
// Criando uma propriedade '.isLong' para as Strings
val String.isLong: Boolean
    get() = this.length > 10

println("Olá".isLong) // false
println("Programação Android".isLong) // true
```

## 6. Conceitos Avançados: Levando a Linguagem ao Limite

Para finalizar, o Kotlin oferece ferramentas para criar código extremamente otimizado e expressivo, que você vai encontrar frequentemente no código-fonte das bibliotecas Android.

### Funções `inline`

No JavaScript, sempre que passas um callback (`.map(x => x * 2)`), a engine JS cria um objeto em memória para essa função. Em ciclos grandes, isso consome recursos (Garbage Collection).No Kotlin, se colocar a palavra `inline` antes da função, o compilador literalmente "copia e cola" o código da sua lambda no local onde a função foi chamada. **Zero alocação de memória**! Quase todas as funções de coleção do Kotlin (`forEach`, `map`, `filter`) são `inline`.

``` kotlin
inline fun repetir(vezes: Int, acao: () -> Unit) {
    for (i in 0 until vezes) {
        acao()
    }
}
```

### Funções com Receptor (Function Literals with Receiver)

No JS, mudar o escopo do `this` dentro de uma função exige `bind()`, `call()` ou `apply()`, o que costuma gerar dores de cabeça. No Kotlin, pode criar uma lambda que "herda" o contexto de um objeto específico. Dentro dessa lambda, o `this` passa a ser o objeto definido.

É isto que faz as funções de escopo nativas (`apply`, `run`, `with`, `let`, `also`) funcionarem.

```kotlin
// O parâmetro 'config' não é apenas '() -> Unit'. 
// Ele é 'StringBuilder.() -> Unit', ou seja, o bloco terá o 'this' de um StringBuilder.
fun construirTexto(config: StringBuilder.() -> Unit): String {
    val builder = StringBuilder()
    builder.config() // Executa o bloco passado pelo utilizador
    return builder.toString()
}

// Uso (repara que não precisamos de escrever 'builder.append()'):
val texto = construirTexto {
    append("Olá ")    // O 'this' aqui é o StringBuilder implicitamente!
    append("Mundo!")
}
```

### Funções Locais (Local Functions)

O Kotlin permite declarar uma função inteira dentro de outra função. É perfeito para extrair lógica repetitiva ou recursiva que só faz sentido para aquela função principal, sem sujar a classe com métodos privados inúteis.

``` kotlin
fun guardarUtilizador(id: String, nome: String, email: String) {
    // Esta função só existe e só pode ser chamada dentro de 'guardarUtilizador'
    fun validar(campo: String, valor: String) {
        if (valor.isBlank()) throw IllegalArgumentException("O campo $campo não pode estar vazio")
    }

    validar("ID", id)
    validar("Nome", nome)
    validar("Email", email)

    println("A guardar utilizador...")
}
```