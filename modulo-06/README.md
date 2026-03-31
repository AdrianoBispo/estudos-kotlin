# 06 - Coleções, Type Aliases e Generics (Kotlin vs TypeScript)

Se você vem do JavaScript, está habituado a manipular arrays com métodos como .`map()`, `.filter()` e `.reduce()`. A excelente notícia é que o Kotlin possui uma biblioteca padrão de coleções incrivelmente rica e funcional, superando em muito os métodos nativos do JS/TS sem necessidade de recorrer a bibliotecas externas como o _Lodash_.

A maior diferença arquitetural que vai encontrar é o conceito de **Imutabilidade de Coleções**. No TypeScript, um array declarado com `const` apenas impede a reatribuição da variável, mas ainda permite alterar o seu conteúdo interno (`.push()`, `.pop()`, `[0] = "novo"`). No Kotlin, a imutabilidade é levada a sério: uma coleção imutável nem sequer possui os métodos de modificação na sua assinatura.

Esta separação garante um código muito mais seguro, livre de efeitos colaterais e perfeitamente preparado para o processamento assíncrono (Coroutines) e interfaces declarativas (Jetpack Compose).

## 1. Listas e Arrays (O fim do `[]` genérico)

No JavaScript, tudo é um simples array `[]`. No Kotlin (que roda sobre a JVM), existe uma diferença de performance e uso entre `Array` e `List`.

#### `Array` vs `List`
- `Array`: Tem um tamanho fixo definido na criação. Traduz-se diretamente para os arrays de baixo nível do Java. Muito rápidos, mas pouco flexíveis. Se quiser otimizar memória ao máximo com números, o Kotlin tem arrays primitivos como `IntArray` e `DoubleArray`.

- `List`: É dinâmico, baseado em interfaces, e é o que vai usar em **99% das vezes** no desenvolvimento Android moderno.

### Listas Imutáveis vs Mutáveis

`listOf()`: Cria uma lista imutável (apenas leitura). Não pode adicionar, remover ou alterar elementos.

`mutableListOf()`: Cria uma lista mutável. Permite adicionar (`.add()`), remover (`.remove()`), alterar (`[index] = valor`).

#### TypeScript:

``` TS
const frutas = ["Maçã", "Banana"];
frutas.push("Laranja"); // Funciona, apesar do 'const'
```

#### Kotlin:

``` kotlin
// Lista Imutável (Apenas leitura)
val frutasImutaveis = listOf("Maçã", "Banana")
// frutasImutaveis.add("Laranja") // ERRO DE COMPILAÇÃO!

// Lista Mutável
val frutasMutaveis = mutableListOf("Maçã", "Banana")
frutasMutaveis.add("Laranja") // Funciona perfeitamente
frutasMutaveis[0] = "Pera"    // Alterar um elemento existente

// Conversão fácil entre os dois mundos!
val novaListaMutavel = frutasImutaveis.toMutableList()
val novaListaImutavel = frutasMutaveis.toList()
```

> **Dica Jetpack Compose:** Vais usar maioritariamente listas imutáveis (`listOf`) para representar o estado da sua UI num `ViewModel`. Quando o utilizador adicionar um item, você não fazes `.add()` na lista existente. Em vez disso, usas o operador `+` para gerar uma lista nova: `val novaLista = listaAntiga + "Novo Item"`. Esta criação de uma nova referência notifica o Compose para redesenhar o ecrã.

## 2. Sets (Conjuntos) e Maps (Dicionários)

Tal como as Listas, os Sets e os Maps também se dividem em versões mutáveis e imutáveis e possuem métodos extremamente seguros para evitar retornos nulos.

#### Set (Valores Únicos)

Um Set é uma coleção que descarta automaticamente valores duplicados. O equivalente direto é o Set do ES6.

``` kotlin
val numeros = setOf(1, 2, 2, 3)
println(numeros) // Imprime: [1, 2, 3]

// Excelente truque para remover duplicados de uma lista:
val listaComDuplicados = listOf("A", "A", "B", "C")
val listaLimpa = listaComDuplicados.toSet().toList() // ou simplesmente .distinct()
```

Map (Chave-Valor)

No TypeScript, usamos frequentemente objetos literais `{ chave: valor }` ou a classe `Map`. No Kotlin, usamos `mapOf()`. A sintaxe para criar pares chave-valor usa a palavra `to` (uma função _infix_ nativa).

``` kotlin
val idades = mapOf(
    "João" to 25,
    "Maria" to 30
)

// O Kotlin oferece maneiras incrivelmente seguras de ler um Map:
println(idades["Maria"]) // Retorna 30
println(idades["Carlos"]) // Retorna null (Não lança erro)

// Retorna um valor padrão se a chave não existir:
println(idades.getOrDefault("Carlos", 18)) // Retorna 18

// O Map Mutável
val config = mutableMapOf<String, String>()
config["tema"] = "Escuro" // Sintaxe de colchetes
config.put("idioma", "pt-PT") // Método clássico
```

## 3. Operações Funcionais (Muito além do `.map` e `.filter`)

A sintaxe funcional do Kotlin tira partido das `Trailing Lambdas` (que vimos no Módulo 04) e do parâmetro implícito `it`. Mas o verdadeiro poder está nos métodos avançados que a biblioteca padrão oferece.

#### TypeScript:
``` TS
const numeros = [1, 2, 3, 4, 5];
const paresDobrados = numeros
    .filter(n => n % 2 === 0)
    .map(n => n * 2);
```

#### Kotlin:
``` kotlin
val numeros = listOf(1, 2, 3, 4, 5)

// Muito mais limpo graças ao 'it' e à sintaxe de lambda!
val paresDobrados = numeros
    .filter { it % 2 == 0 }
    .map { it * 2 }
```

### O Paraíso do Processamento de Dados

No JavaScript, fazer agrupamentos muitas vezes exige um .reduce() complexo. No Kotlin, temos funções prontas para praticamente tudo:

``` kotlin
val utilizadores = listOf(
    Utilizador("Ana", idade = 25, cidade = "Porto"),
    Utilizador("João", idade = 30, cidade = "Lisboa"),
    Utilizador("Maria", idade = 25, cidade = "Porto")
)

// 1. groupBy: Agrupa itens num Map usando a chave que definires!
// Retorna: Map<String, List<Utilizador>> (Chave: Cidade, Valor: Lista de Utilizadores)
val agrupadosPorCidade = utilizadores.groupBy { it.cidade }

// 2. partition: Divide a lista em duas baseado numa condição!
// Retorna: Um Pair com duas listas (A primeira atende à condição, a segunda não)
val (jovens, adultos) = utilizadores.partition { it.idade < 28 }

// 3. Funções de Busca (Com Null Safety):
val maria = utilizadores.find { it.nome == "Maria" } // Retorna Utilizador ou null
val todosDeLisboa = utilizadores.all { it.cidade == "Lisboa" } // false
val alguemDeLisboa = utilizadores.any { it.cidade == "Lisboa" } // true

// 4. Transformações avançadas:
// associateBy: Transforma a sua lista num Map, usando uma propriedade como Chave.
// Ideal para converter listas da API em dicionários indexados para buscas rápidas (O(1)).
val mapaPorNome = utilizadores.associateBy { it.nome } 
// mapaPorNome["João"] -> Devolve o objeto inteiro do João
```

## 4. Type Aliases (Apelidos de Tipos)

No TypeScript, usamos muito a palavra `type` para dar nomes mais semânticos a tipos complexos ou criar "atalhos". O Kotlin possui uma funcionalidade exatamente igual chamada `typealias`.

A grande utilidade do `typealias` é simplificar a leitura de Generics muito aninhados. No Kotlin, isto deve ser declarado ao nível do ficheiro (fora das classes).

#### TypeScript:

``` TS
type UserId = string;
type MapaDePermissoes = Map<string, Array<string>>;
```

#### Kotlin:

``` kotlin
typealias UserId = String
typealias MapaDePermissoes = Map<String, List<String>>

// Funções lambdas complexas ficam muito mais legíveis
typealias OnUsuarioClicado = (UserId, Boolean) -> Unit

fun configurarBotao(onClick: OnUsuarioClicado) { ... }
```

## 5. Generics (`<T>`) e a Magia do `reified`

Generics permitem escrever código que funciona com diferentes tipos, mantendo a segurança rigorosa do sistema de tipos. A sintaxe de transição do TypeScript para o Kotlin é praticamente 1 para 1.

``` kotlin
// Generics numa Classe
class Caixa<T>(val conteudo: T) {
    fun obterConteudo(): T = conteudo
}

val caixaDeTexto = Caixa("Documento Secreto") // O Kotlin infere Caixa<String>
```

### O Superpoder: Generics Reificados (`reified`)

No TypeScript (e no Java padrão), existe um fenómeno chamado `Type Erasure` (Apagamento de Tipo). Isto significa que em tempo de execução, a linguagem "esquece" o que era o `<T>`. Você não pode fazer `if (dado instanceof T)` no TypeScript.

O Kotlin ultrapassa esta limitação usando funções `inline` combinadas com a palavra `reified`. Isto permite que **o Kotlin guarde o tipo `<T>` em tempo de execução**, o que é absolutamente incrível para mapear respostas de APIs (ex: JSON para Objeto) e navegação entre ecrãs.

``` kotlin
// Sem o 'inline' e o 'reified', o Kotlin não saberia o que é o 'T' durante a execução.
// Com eles, o Kotlin sabe exatamente a classe que foi passada!
inline fun <reified T> analisarJSON(json: String): T {
    println("A analisar o JSON e a converter para a classe: ${T::class.simpleName}")
    // Lógica fictícia que usa a biblioteca Gson ou Moshi
    return Gson().fromJson(json, T::class.java)
}

// Uso limpo e elegante:
val utilizador = analisarJSON<Utilizador>("{ 'nome': 'Carlos' }")
val produto = analisarJSON<Produto>("{ 'preco': 19.99 }")
```
