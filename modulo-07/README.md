# Módulo 07: Corrotinas (Coroutines) e Programação Assíncrona

No JavaScript e TypeScript, a programação assíncrona é majoritariamente resolvida usando **Promises** e as palavras-chave `async` e `await`. O Kotlin oferece um paradigma semelhante, mas extremamente leve e poderoso, conhecido como **Corrotinas (Coroutines)**.

Enquanto uma Promise no mundo JavaScript é baseada em callbacks e a "Thread Principal" (Event Loop), as Corrotinas do Kotlin são chamadas de *threads leves*. Elas permitem escrever código assíncrono de forma sequencial sem bloquear a thread real do sistema operacional.

## O Equivalente a `async/await` no Kotlin

### Funções Suspensas (`suspend fun`)

A base das corrotinas é a palavra-chave `suspend`. Uma função marcada com `suspend` é análoga a uma função declarada como `async` no JavaScript/TypeScript: ela avisa ao compilador que a sua execução, em algum momento, pode precisar aguardar sem bloquear o restante do programa. Em essência, funções `suspend` só podem ser chamadas a partir de outras funções `suspend` ou de blocos construtores de corrotina (`Coroutine builders`).

```kotlin
// TypeScript:
// async function fetchData(): Promise<string> {
//     const response = await fetch("https://api.exemplo.com/dados");
//     return response.text();
// }

// Kotlin:
import kotlinx.coroutines.delay

suspend fun fetchData(): String {
    // delay(1000) é análogo a um setTimeout() encapsulado numa Promise
    delay(1000) 
    return "Dados Retornados da API"
}
```

## Explorando os Coroutine Builders (`launch` x `async`)

Para iniciar uma corrotina, você precisa de um "escopo" (`CoroutineScope`) e um bloco construtor ("builder"). Dois dos principais builders são:

### 1. `launch` (Não Retorna Valor - "Fire and Forget")

É ideal quando não precisamos de um valor de retorno. Em JavaScript/TypeScript, equivaleria a invocar uma função assíncrona que retorna `Promise<void>`, não aguardando seu término se você não usar `await`.

```kotlin
import kotlinx.coroutines.*

fun main() {
    // 'runBlocking' simula o bloqueio da thread principal para manter a JVM viva
    // enquanto aguarda as corrotinas filhas terminarem
    runBlocking {
        launch { 
            val dados = fetchData()
            println(dados) // Printa após 1 segundo
        }
        println("Isso imprime antes da corrotina terminar")
    }
}
```

### 2. `async` / `await` (Quando se espera um Retorno)

Se usamos `async` num TypeScript, ele nos retorna uma `Promise<T>`. No Kotlin, o builder `async` retorna uma instância do tipo `Deferred<T>` (uma espécie de "Promise" nativa do framework que também suporta suspensão). 
Para pegarmos esse valor `T` futuramente (o momento que resolver), usamos `.await()`.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    val dadosAssincronos = async {
        fetchData() // Retorna "Deferred<String>"
    }
    
    // Aguardamos que o processamento do async termine para ler o valor
    val resultado: String = dadosAssincronos.await() 
    println("Resultado: $resultado")
}
```

## Fluxos Baseados no Tempo: Coroutine Dispatchers

Quando criamos corrotinas, podemos também atribuir um "lugar onde devem rodar" através de **Dispatchers** (despachantes). Isso não possui paralelo com o mundo Node.js ou navegador JavaScript, que geralmente são de *thread única* (alheio aos Web Workers). O Kotlin, por conta da JVM ou em ecossistemas nativos, suporta multi-threading fluido.

* **`Dispatchers.Main`**: Utilizado para atualizar a Interface de Usuário (ex: Jetpack Compose no Android).
* **`Dispatchers.IO`**: Otimizado para operações intensivas de CPU e IO fora da tela principal (ex: leitura de disco, chamadas de rede em servidores ou apps, banco de dados).
* **`Dispatchers.Default`**: Ideal para processamentos pesados ​​de CPU.

### Exemplo (Context Switch):
*Mudando contextos para requisições não obstruírem interfaces.*

```kotlin
import kotlinx.coroutines.*

suspend fun atualizarUsuarioDaBase() = withContext(Dispatchers.IO) {
    // Estamos dizendo que este bloco RODA NA THREAD 'IO', garantindo a integridade e rapidez em Android UI ou servidores.
    val query = "SELECT * FROM users"
    delay(500) // Simulação de consulta ao banco de dados
    return@withContext "Usuário Atualizado" 
}
```

## Resumo JS/TS ➡️ Kotlin

| TypeScript / JavaScript              | Kotlin      |
| ------------------------------------ | :-------- |
| `async function() { ... }`           | `suspend fun()` |
| `Promise<T>`                         | `Deferred<T>` (retornado pelo `async { }`) |
| `await minhaVariavelAssincrona`      | `minhaVariavelAssincrona.await()` |
| `setTimeout()` num invólucro Promise | `delay()` |
| `Promise.all([p1, p2])`              | `awaitAll(d1, d2)` |
