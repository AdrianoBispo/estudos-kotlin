# 03 - Null Safety (Segurança contra Nulos)

Se você vem do JavaScript, está habituado a lidar com `null` e `undefined` a todo o momento, e provavelmente já perdeste horas a depurar o clássico erro `Cannot read properties of undefined`. No TypeScript, conseguimos mitigar parte desse problema usando Union Types (como `string | null`) e ativando a flag `strictNullChecks` no `tsconfig.json`, mas ainda assim, é uma verificação que pode ser contornada.

No Kotlin, o conceito de `undefined` **não existe**. Uma variável ou tem um valor, ou é `null`. E a regra de ouro do Kotlin é: **Por defeito, nenhuma variável pode receber** `null`.

O sistema de tipos do Kotlin foi desenhado desde o primeiro dia para eliminar o que _Tony Hoare_ (o criador da referência nula) chamou de "O Erro de Mil Milhões de Dólares". No tempo em que o Android era programado estritamente em Java, o `NullPointerException` (NPE) era a causa número um de _crashes_ nas aplicações. O Kotlin resolve isto fazendo com que a nulidade faça parte do sistema de tipos no momento da **compilação**, não apenas na execução.

## 1. Tipos Anuláveis (`?`) vs Não Anuláveis

Para permitir que uma variável receba `null` no Kotlin, tem de declarar explicitamente o tipo adicionando um ponto de interrogação (`?`) no final. O compilador trata `String` e `String?` como dois tipos completamente diferentes e incompatíveis de forma direta.

#### TypeScript:

``` TS
let nome: string = "Maria";
// nome = null; // Erro se o strictNullChecks estiver ativo

let apelido: string | null = "Silva";
apelido = null; // Permitido
```

#### Kotlin:

``` kotlin
var nome: String = "Maria"
// nome = null // ERRO DE COMPILAÇÃO! Tipos normais não aceitam null.

// Para aceitar nulo, usamos o tipo acompanhado de '?'
var apelido: String? = "Silva"
apelido = null // Perfeitamente válido!
```

### O Desafio das Coleções e Nulidade (Atenção aqui!)

Quando trabalhamos com Listas ou Arrays, o uso do `?` pode ser colocado em dois lugares diferentes, e isso muda tudo. É um conceito que confunde muitos programadores no início:

``` kotlin
// 1. A lista NÃO PODE ser nula, mas os itens LÁ DENTRO PODEM ser nulos.
val listaComItensNulos: List<String?> = listOf("Maçã", null, "Banana")

// 2. A LISTA EM SI pode ser nula, mas se existir, os itens não podem ser nulos.
val listaNula: List<String>? = null

// 3. O caos: A lista pode ser nula E os itens podem ser nulos.
val listaTotalmenteNula: List<String?>? = null
```

No Jetpack Compose, dominar essa diferença é vital, especialmente ao desenhar ecrãs que dependem de listas vindas de uma API que pode falhar ou retornar dados incompletos.

## 2. Operadores de Segurança: Navegando em Nulos

Quando tem uma variável anulável (`String?`), o Kotlin **proíbe-te** de aceder aos seus métodos ou propriedades diretamente. O compilador obriga-te a tratar o caso nulo.

### Safe Call (`?.`) - O Optional Chaining do Kotlin

A sintaxe é exatamente igual à do TypeScript! Se a referência antes do `?.` for nula, a expressão inteira retorna `null` imediatamente, sem avaliar o resto, e previne o erro.

``` kotlin
// Acede à cidade apenas se o utilizador, a morada e a cidade não forem nulos.
val nomeCidade = utilizador?.morada?.cidade?.nome
```

### O Casamento Perfeito: `?.let { }`

No TypeScript, faríamos um `if (usuario?.nome) { fazerAlgo(usuario.nome) }`. No Kotlin, o padrão mais idiomático e poderoso da linguagem é combinar o Safe Call (`?.`) com a função de escopo `let`. O bloco de código dentro do `let` **só será executado se o objeto não for nulo**, _e o valor não-nulo é passado para dentro do bloco como_ `it`.

``` kotlin
var emailDigitado: String? = "teste@exemplo.com"

// Só vai imprimir e guardar na base de dados se o emailDigitado NÃO for nulo.
emailDigitado?.let { emailSeguro ->
    println("O email é: $emailSeguro")
    baseDeDados.guardar(emailSeguro)
}
```

### Operador Elvis (?:) - O Nullish Coalescing do Kotlin

No TypeScript, usamos `??` para fornecer um valor por padrão. No Kotlin, usamos o **Operador Elvis** `?:`. Ele avalia o lado esquerdo; se não for nulo, devolve-o. Se for nulo, devolve o que estiver do lado direito.


#### TypeScript (`??`):

``` TS
const nomeExibicao = utilizador.apelido ?? "Visitante";
```

#### Kotlin (`?:`):

``` kotlin
val nomeExibicao = utilizador.apelido ?: "Visitante"

// O Operador Elvis também pode lançar erros ou retornar de uma função prematuramente!
fun processarUtilizador(id: String?) {
    // Se o id for nulo, a função aborta a execução imediatamente (Early Return).
    val idSeguro = id ?: return 
    
    // Se o nome for nulo, lança uma exceção personalizada.
    val nome = buscarNome(idSeguro) ?: throw IllegalArgumentException("Nome não encontrado!")
}
```

## 3. O Perigoso `!!` (Non-null Assertion) e suas Alternativas

Às vezes, você ten a certeza absoluta de que uma variável anulável tem um valor real naquele momento específico, mas o compilador não consegue deduzir isso. No TypeScript usamos o ponto de exclamação (`!`). No Kotlin, usamos dois (`!!`).

O `!!` converte qualquer valor para o seu tipo não nulo à força, mas **lança um** `NullPointerException` **se o valor for nulo.**

#### Kotlin (!!):

```kotlin
var nome: String? = "Carlos"
val tamanho = nome!!.length // Força a execução. Funciona porque 'nome' tem valor.

nome = null
// val erro = nome!!.length // CRASH! Lança NPE e a aplicação falha instantaneamente.
```

- **⚠️ Aviso:** O aspeto visual dele (dois sinais de exclamação) foi desenhado para parecer um alerta ou alguém a gritar no código. Se vires `!!` no seu código, é um code smell (mau cheiro).

### Alternativa Segura: `requireNotNull()` ou `checkNotNull()`

Em vez de usar `!!` (que lança um erro genérico e difícil de rastrear no log), o Kotlin fornece funções explícitas que lançam erros com mensagens claras.

```kotlin
// Em vez disto: val token = auth.token!!
// Usa isto:
val token = requireNotNull(auth.token) { "O token de autenticação não pode ser nulo nesta fase!" }
```

## 4. Smart Casts (Inferência Inteligente) e as suas Limitações

O Kotlin tem um compilador extremamente inteligente. Se você verificare manualmente que uma variável não é nula usando um simples `if`, dentro desse bloco o Kotlin converte automaticamente a variável de `Tipo?` para `Tipo` (exatamente como o _Type Narrowing_ do TypeScript).

```kotlin
fun processarTexto(texto: String?) {
    if (texto != null) {
        // Smart Cast: 'texto' agora é considerado String (não nulo).
        println("O texto tem ${texto.length} caracteres.")
    }
}
```

### 🛑 O Grande "Gotcha" (Armadilha) do Smart Cast


O Smart Cast só funciona se o compilador tiver a `certeza absoluta` de que a variável não pode ser alterada por outra thread (ou outro pedaço de código) entre o momento da verificação e o momento do uso.

Se a sua variável for um `var` global ou pertencer a uma classe (propriedade mutável), o compilador **vai recusar o Smart Cast.**

``` kotlin
class Processador {
    var dadoMutavel: String? = null // É um 'var' na classe

    fun executar() {
        if (dadoMutavel != null) {
            // ERRO DE COMPILAÇÃO!
            // O compilador diz: "Smart cast is impossible, because 'dadoMutavel' is a mutable property that could have been changed by this time"
            // println(dadoMutavel.length) 
        }
    }
}
```

#### Como resolver isto?

1. Usa o `?.let { }` (como vimos no ponto 2).

2. Atribui a uma variável local imutável (`val`) antes do `if`:

``` kotlin
val copiaLocal = dadoMutavel
if (copiaLocal != null) {
    println(copiaLocal.length) // Agora o Smart Cast funciona!
}
```

## 5. Inicialização Tardia (`lateinit` e `by lazy`)

No Android, muitas propriedades não podem ser inicializadas no momento exato em que a classe é criada, porque dependem do ciclo de vida do ecrã (como quando um Componente precisa ser renderizado antes de obtermos o seu tamanho).

Para evitar ter de declarar essas variáveis como anuláveis (`var algo: Tipo? = null`) e encher o código com `?.`, usamos soluções de inicialização diferida.

### Promessa de Inicialização (`lateinit var`)

O `lateinit` (late initialization) diz ao compilador: _"Confia em mim, prometo inicializar esta variável antes de alguém tentar aceder a ela"_.
- Só funciona com `var` (mutável).
- Só funciona com tipos não primitivos (não pode usar com `Int`, `Double`, `Boolean`).

``` kotlin
class MeuEcra {
    lateinit var configuracaoDoEcra: String

    fun inicializar() {
        configuracaoDoEcra = "Modo Escuro"
    }

    fun exibir() {
        // Opcional: Podes verificar se a variável já foi inicializada para evitar crashes!
        if (::configuracaoDoEcra.isInitialized) {
            println(configuracaoDoEcra) 
        } else {
            println("Ainda a carregar configurações...")
        }
    }
}
```

### Inicialização Preguiçosa e Thread-Safe (`val ... by lazy`)

O `by lazy` diz ao sistema: _"Não calcules ou instancies isto agora. Faz isso apenas e somente na primeira vez que alguém pedir o valor"_. O resultado é guardado em cache para utilizações futuras.
- Só funciona com `val` (imutável).
- **É Thread-Safe por padrão:** Se múltiplas threads tentarem aceder à variável ao mesmo tempo pela primeira vez, o Kotlin garante que o bloco de inicialização será executado apenas uma vez.

``` kotlin 
class ProcessadorDeDados {
    val baseDeDados: BaseDeDadosPesada by lazy {
        println("A instanciar a base de dados... Isto só acontece uma vez!")
        BaseDeDadosPesada() 
    }

    fun obterDados() {
        // Na primeira chamada, executa o bloco acima e retorna o objeto.
        // Nas chamadas seguintes, retorna o objeto guardado em memória imediatamente.
        baseDeDados.consultar() 
    }
}
```