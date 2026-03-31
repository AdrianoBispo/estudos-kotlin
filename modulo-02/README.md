# 02 - Controle de Fluxo (Kotlin vs TypeScript)

Neste módulo, vamos ver como o Kotlin toma decisões e repete ações. Se no JavaScript o `switch/case` costuma ser verboso e evitado por muitos (muitas vezes substituído por literais de objeto ou dicionários), no Kotlin você vai se apaixonar pelo `when`.

O conceito mais transformador que você deve absorver aqui é a diferença entre `Declarações (Statements)` e `Expressões (Expressions)`. No JavaScript, estruturas de controle são apenas declarações: elas executam um bloco de código, mas não geram um valor. No Kotlin, estruturas de controle como `if`, `when` e `try` podem ser expressões, o que significa que elas avaliam um resultado e o retornam. Isso muda completamente a forma como escrevemos um código limpo, imutável e direto, reduzindo drasticamente a necessidade de declarar variáveis mutáveis (`let` / `var`) apenas para armazenar resultados de condicionais.

## 1. Estruturas Condicionais (`if/else`) e o Fim do Operador Ternário

A sintaxe básica do `if/else` é exatamente igual à do JavaScript, TypeScript, Java ou C#. Ele avalia uma condição booleana e executa o bloco correspondente.

#### Kotlin (Uso Tradicional como Declaração):

``` kotlin
val idade = 20
if (idade >= 18) {
    println("Maior de idade")
} else {
    println("Menor de idade")
}
```

### ⚠️ Onde está o Operador Ternário (`? :`)?

No TypeScript, o operador ternário é o rei das atribuições condicionais rápidas:

``` TS
const status = idade >= 18 ? "Maior" : "Menor";
```

No Kotlin, o operador ternário não existe. A decisão de design da linguagem foi simples: por que ter um operador extra se o próprio `if` pode fazer esse trabalho de forma mais legível? O `if` no Kotlin é uma expressão. A última linha de código avaliada dentro do bloco do `if` ou `else` torna-se o valor de retorno.

#### Kotlin (If como Expressão):

``` kotlin
val idade = 20

// O resultado do 'if' é atribuído diretamente à variável 'status'
val status = if (idade >= 18) "Maior de idade" else "Menor de idade"

// Também funciona perfeitamente com blocos de código maiores:
val mensagem = if (idade >= 18) {
    println("A verificar no sistema...")
    // Lógica complexa poderia acontecer aqui
    "Acesso Permitido" // Esta última linha será o valor armazenado em 'mensagem'
} else {
    println("Bloqueando usuário...")
    "Acesso Negado"
}
```

- **Dica de Ouro:** Ao usar o `if` como expressão (atribuindo o resultado a uma variável), o uso do `else` passa a ser obrigatório. O compilador precisa ter certeza de que a variável receberá um valor em qualquer cenário possível.

## 2. O Poder do `when` (O `switch/case` com esteroides)

No TypeScript, o `switch/case` é conhecido por ser verboso, propenso a erros (se esquecer o `break`, ocorre o temido **fallthrough**, executando os casos abaixo) e é limitado a comparar valores exatos por igualdade.

No Kotlin, usamos o `when`. Ele é infinitamente mais flexível, seguro, e não precisa de `break` (por padrão, apenas o bloco correspondente é executado).

#### TypeScript (`switch` tradicional):

``` TS
const tipoUsuario = "ADMIN";

switch (tipoUsuario) {
    case "ADMIN":
    case "SUPER_ADMIN":
        console.log("Acesso total");
        break;
    case "USER":
        console.log("Acesso restrito");
        break;
    default:
        console.log("Desconhecido");
}
```

#### Kotlin (`when` com argumento):

``` kotlin
val tipoUsuario = "ADMIN"

when (tipoUsuario) {
    "ADMIN", "SUPER_ADMIN" -> println("Acesso total") // Vírgula agrupa as condições!
    "USER" -> println("Acesso restrito")
    else -> println("Desconhecido") // O 'else' é o equivalente ao 'default'
}
```

### Casos de Uso Avançados do `when`

O `when` não serve apenas para comparar valores exatos. Você pode realizar lógicas complexas, verificar tipos de dados ou checar intervalos, tudo de forma muito expressiva. E, assim como o `if`, ele também pode retornar valores.
 
#### Veja o exemplo abaixo:

``` kotlin
val nota = 85

// 1. Verificando intervalos (Ranges) e retornando valor
val conceito = when (nota) {
    in 90..100 -> "A" // Se a nota estiver entre 90 e 100 (inclusivo)
    in 80..89 -> "B"
    in 70..79 -> "C"
    !in 0..100 -> "Nota Inválida" // Podemos usar a negação '!in'
    else -> "Reprovado" // 'else' é OBRIGATÓRIO quando usado como expressão
}

// 2. when SEM argumento (Funciona como uma cadeia de if-else if muito mais limpa)
// Útil quando as condições não dependem de uma única variável.

val temperatura = 30
val estaChovendo = true

when {
    temperatura < 0 -> println("A congelar")
    temperatura > 20 && estaChovendo -> println("Calor úmido, leve guarda-chuva")
    temperatura in 0..20 -> println("Frio moderado")
}

// 3. Verificando Tipos de Dados (Equivalente ao 'typeof' ou 'instanceof' do TS)
// Isso é extremamente comum ao consumir APIs onde o payload pode variar.

val dado: Any = "Kotlin"
when (dado) {
    is String -> println("Texto com ${dado.length} caracteres") // Smart cast automático para String!
    is Int -> println("É um número inteiro e o dobro é ${dado * 2}")
    is List<*> -> println("É uma lista com ${dado.size} itens")
    else -> println("Tipo desconhecido")
}
```

### A Segurança do `when` Exaustivo (Exhaustive when)

No TypeScript, garantir que cobrimos todos os estados possíveis de uma _Union Type_ exige hacks (como atribuir o valor a uma variável do tipo `never`). No Kotlin, se você usar o `when` como expressão com _Enum Classes_ ou _Sealed Classes_ (que veremos no módulo de POO), o compilador força você a mapear todos os casos possíveis. Se adicionar um novo estado no futuro, o código nem compila até você atualizar o `when`! É o melhor amigo da refatoração segura.

## 3. Laços de Repetição (`for`, `while`, `do..while`)

Enquanto o JavaScript possui o `for (let i = 0; i < 10; i++)`, o Kotlin modernizou isso focando-se na iteração sobre iteráveis (como o `for...of` do TS) e no uso de _Ranges_ (Intervalos).

#### Veja os exemplos abaixo do uso do laço `for`: 

``` TS
// TypeScript

const frutas = ["Maçã", "Banana", "Uva"];
for (const fruta of frutas) {
    console.log(fruta);
}
```

``` kotlin
// Kotlin

val frutas = listOf("Maçã", "Banana", "Uva")

// Iterar sobre os itens (Equivalente ao for...of)
for (fruta in frutas) {
    println(fruta)
}

// Iterar necessitando do índice
for ((indice, fruta) in frutas.withIndex()) {
    println("Posição $indice: $fruta")
}

// Iterar usando Ranges numéricos
for (i in 0..5) print(i) // 012345 (O '5' está incluído)
for (i in 0 until 5) print(i) // 01234 (O '5' NÃO está incluído, ideal para iterar arrays)
for (i in 10 downTo 1 step 2) print(i) // 108642 (Contagem regressiva pulando de 2 em 2)
```

#### Veja os exemplos abaixo do uso dos Laços `while` e `do..while`: 

A sintaxe aqui é rigorosamente idêntica ao JavaScript/TypeScript.

``` kotlin
var contador = 5
while (contador > 0) {
    println("Contagem: $contador")
    contador--
}

do {
    // Executa pelo menos uma vez, mesmo se a condição for falsa de imediato.
    println("Isto executa pelo menos uma vez")
} while (false)
```

**Controle de Fluxo Fino:** `break`, `continue` e **Labels**

Assim como no TS, o `break` sai do laço e o `continue` pula para a próxima iteração. No entanto, o Kotlin possui **Labels** (rótulos) que permitem controlar laços aninhados de forma muito elegante.

``` kotlin
// Define um rótulo chamado 'meuLoopExterno'
meuLoopExterno@ for (i in 1..3) {
    for (j in 1..3) {
        if (i == 2 && j == 2) {
            break@meuLoopExterno // Quebra o laço externo, não apenas o laço interno!
        }
        println("i = $i, j = $j")
    }
}
```

**Nota Funcional:** Apesar das estruturas acima, no Kotlin é extremamente comum o uso de funções de escopo em coleções como `.forEach { }`, `.map { }` e `.filter { }`, aproximando-se muito do paradigma funcional comum no TypeScript moderno.

## 4. Controle de Exceções (`try/catch/finally`)

A estrutura básica para capturar erros é a mesma. No entanto, o Kotlin possui duas grandes vantagens em relação ao TypeScript/Java:

- **Não existem "Checked Exceptions":** No Java, você era obrigado a tratar certos erros ou declarar na assinatura da função que ela poderia falhar. O Kotlin (assim como o TS) não te obriga a isso. Você usa o `try/catch` apenas quando faz sentido para a sua lógica de negócio. O `try` também é uma expressão. Segue o exemplo abaixo:

``` kotlin

val texto = "123a"

// Try tradicional (Declaração)
try {
    val numero = texto.toInt()
    println("Número convertido: $numero")
} catch (e: NumberFormatException) {
    println("Erro: O texto não é um número válido.")
} finally {
    // Ótimo para fechar conexões, limpar memória ou parar um loading
    println("Este bloco executa sempre, ocorrendo erro ou não.")
}

// Try como Expressão (Fantástico para inicializar variáveis com segurança)
val idUsuario: Int? = try {
    texto.toInt() // Tenta converter
} catch (e: NumberFormatException) {
    null // Se falhar, a variável idUsuario recebe 'null' em vez de travar o app
}
```

### Lançando Erros (`throw` e o operador Elvis `?:`)

No TS, usamos `throw new Error()`. No Kotlin, não existe a palavra `new`, então fazemos apenas `throw Exception()`. O mais legal é que o `throw` pode ser combinado com o _Operador Elvis_ (que veremos no próximo módulo) para criar guardas (Guard Clauses) muito enxutas:

``` kotlin
// Se usuario.nome for null, a execução para imediatamente e lança o erro.
val nomeValido = usuario.nome ?: throw IllegalArgumentException("Nome é obrigatório!")
```