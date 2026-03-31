# 01 - Fundamentos Básicos (Kotlin vs TypeScript)

Neste módulo, vamos abordar a base da linguagem Kotlin. Se você vem do ecossistema JavaScript/TypeScript, vai notar que o Kotlin possui uma sintaxe muito limpa e moderna, com um forte sistema de inferência de tipos.

Diferente do JavaScript, que é interpretado (ou compilado Just-In-Time por motores como o V8), o Kotlin (no contexto do Android) é compilado estaticamente para rodar na JVM (Java Virtual Machine) ou no Android Runtime (ART). Isso significa que o compilador do Kotlin é o seu melhor amigo: ele é extremamente rigoroso e vai capturar a maioria dos erros antes mesmo de você rodar a aplicação, oferecendo uma segurança que no mundo Web nós geralmente só alcançamos configurando o TypeScript de forma muito estrita.

## 1. Variáveis e Imutabilidade (`val` vs `var`)

No JavaScript moderno, aprendemos a usar `const` por padrão e `let` apenas quando a variável precisa de ter o seu valor reatribuído. No Kotlin, a regra de ouro é exatamente a mesma, mas com palavras-chave diferentes:

- `val` **(Value):** É o equivalente ao `const` do JS. Uma vez atribuído, o valor não pode ser alterado (imutável). É a recomendação padrão no Kotlin.

- `var` **(Variable):** É o equivalente ao `let` do JS. O valor pode ser reatribuído (mutável).

#### TypeScript:
``` TS
const nome: string = "João";
// nome = "Carlos"; // Erro: Assignment to constant variable.

let idade: number = 25;
idade = 26; // Funciona perfeitamente
```

#### Kotlin:

``` Kotlin
val nome: String = "João"
// nome = "Carlos" // Erro: Val cannot be reassigned

var idade: Int = 25
idade = 26 // Funciona perfeitamente
```

### ⚠️ O Pulo do Gato: Imutabilidade de Referência vs. Valor

Assim como no JavaScript (onde um objeto declarado com `const` pode ter as suas propriedades alteradas), o `val` no Kotlin garante apenas que a **referência** não muda. Se o objeto apontado pela variável for mutável, você pode alterar o seu conteúdo interno.

#### Kotlin:

``` kotlin
// Usamos 'val', ou seja, não podemos reatribuir a variável 'lista'
val lista = mutableListOf("Maçã", "Banana")

// Mas podemos modificar o conteúdo interno da lista!
lista.add("Laranja") // Funciona perfeitamente

// lista = mutableListOf("Uva") // ERRO! Não podemos apontar para uma nova lista.
```

**Dica:** Repare que no Kotlin não precisamos de usar ponto e vírgula (`;`) no final das linhas! Só usamos se quisermos colocar duas instruções na mesma linha (o que não é recomendado).

## 2. Tipos de Dados Básicos e Inferência

No TypeScript, usamos maioritariamente `string`, `number` e `boolean`. No Kotlin, tudo é um objeto (não existem tipos primitivos em minúsculas como `int` ou `double` do Java antigo).

Além disso, como estamos num ambiente mobile onde o uso de memória importa, o tipo `number` do TS divide-se em vários tipos no Kotlin.

### Mapeamento TS -> Kotlin

- `string` ➡️ `String` (Sempre com letra maiúscula).
- `boolean` ➡️ `Boolean`.
- `number` (Inteiros) ➡️ `Int` (32 bits - padrão) ou `Long` (64 bits - para números muito grandes, como IDs de banco de dados ou timestamps).
- `number` (Decimais) ➡️ `Double` (64 bits - padrão) ou `Float` (32 bits - menos preciso, mas ocupa menos memória).
- **Novo:** `Char`. No JS não existe um tipo para "um único caractere", tudo é string. No Kotlin, caracteres únicos usam aspas simples (`'A'`), e strings usam aspas duplas (`"Android"`).

### Tipos Especiais (`Any` e `Unit`)
- `any` ou `unknown` do TS ➡️ `Any` no Kotlin. (A raiz de todos os tipos. Qualquer variável pode ser do tipo `Any`).
- `void` do TS ➡️ `Unit` no Kotlin. (Significa que uma função não retorna nada útil).

Assim como o TypeScript, o Kotlin é inteligente o suficiente para inferir o tipo da variável se a inicializar logo de imediato.

#### TypeScript:

``` TS
const cidade = "Recife"; // Inferred as string
const populacao = 1500000; // Inferred as number
const isCapital = true; // Inferred as boolean
```

#### Kotlin:

``` kotlin
val cidade = "Recife" // Inferido como String
val populacao = 1_500_000 // Inferido como Int (Podemos usar underlines para facilitar a leitura!)
val isCapital = true // Inferido como Boolean
val inicial = 'R' // Inferido como Char (Aspas simples!)

// Se precisar de tipar explicitamente ou usar tipos específicos:
val preco: Double = 19.99
val altura: Float = 1.75f // O 'f' ou 'F' no final indica que é um Float e não um Double
val idBanco: Long = 9876543210L // O 'L' indica que é um Long
```

## 3. Operadores Lógicos e Igualdade (Atenção aqui!)

Os operadores lógicos básicos (`&&` para **AND**, `||` para **OR**, `!` para **NOT**) funcionam exatamente da mesma forma que no JavaScript, inclusive com o conceito de _short-circuit_ (se a primeira condição do **&&** for falsa, ele nem avalia a segunda).

### A grande diferença está na Igualdade (`==` vs `===`):

No **JavaScript/TypeScript:**

- `==` compara valores (fazendo coerção de tipos silenciosa, o que costuma dar problemas graves de lógica).

- `===` compara valores E tipos estritamente. É a recomendação padrão.

No **Kotlin** é diferente (e muito mais seguro):

- `==` **(Igualdade Estrutural):** Compara o `conteúdo/valor`. O Kotlin traduz o `==` por debaixo dos panos para a função `.equals()`. É perfeitamente seguro e encorajado usar `==` no Kotlin para comparar o texto de strings ou dados de objetos (Data Classes)! O Kotlin não faz coerção maluca de tipos como o JS (não pode comparar um `String` com um `Int` usando `==`, o compilador nem deixa).

- `===` **(Igualdade Referencial):** Compara se as duas variáveis apontam para o **mesmo espaço de memória** (a mesma exata instância do objeto).

#### Kotlin:

``` kotlin
val texto1 = "Olá"
val texto2 = "Olá"

println(texto1 == texto2)  // true (Os valores/caracteres são iguais)
println(texto1 === texto2) // true (Por otimização da JVM, apontam para a mesma pool de strings)

val nome1 = String(charArrayOf('j', 'o', 'a', 'o'))
val nome2 = String(charArrayOf('j', 'o', 'a', 'o'))

println(nome1 == nome2)  // true (O conteúdo é o mesmo! O '==' do Kotlin é seguro)
println(nome1 === nome2) // false (São instâncias diferentes criadas na memória)

// Diferentes também existem:
// != (Desigualdade estrutural)
// !== (Desigualdade referencial)
```

## 4. Operadores Matemáticos e Manipulação de Strings

A matemática básica no Kotlin é idêntica ao JavaScript.
- **Adição:** `+`
- **Subtração:** `-`
- **Multiplicação:** `*`
- **Divisão:** `/`
- **Resto (Módulo):** `%`
- **Incremento/Decremento:** `++`, `--`

#### Kotlin:

``` kotlin
var contador = 10
contador++ // 11
contador += 5 // 16

val resto = 10 % 3 // 1
```

### ⚠️ Cuidado com as Divisões de Inteiros

Como o Kotlin diferencia fortemente inteiros de decimais (ao contrário do JS, onde tudo é `number`), dividir dois inteiros resulta num inteiro (ele simplesmente corta e descarta a parte decimal, não arredonda).

``` kotlin
val div = 5 / 2 // O resultado será `2` (Int). 
// No JS, seria 2.5.

// Para ter o valor decimal exato no Kotlin, pelo menos um dos números deve ser decimal:
val divExata1 = 5.0 / 2 // Resultado: `2.5` (Double).
// Ou convertendo uma variável de forma explícita:
val a = 5
val b = 2
val divExata2 = a.toDouble() / b // Resultado: 2.5
```

### Concatenação e Template Strings

No JavaScript/TypeScript moderno, abandonamos a concatenação com `+` em favor dos _Template Literals_ usando crases (**\` \`**). No Kotlin, o conceito é o mesmo, mas a sintaxe é ainda mais limpa, usando aspas duplas normais e o símbolo `$`.

#### TypeScript:

``` TS
const linguagem = "TypeScript";
const frase = `Eu programo em ${linguagem} há ${2 + 2} anos.`;
```

#### Kotlin:

``` kotlin
val linguagem = "Kotlin"
// Se for apenas uma variável simples, usamos apenas o $.
val frase = "Eu programo em $linguagem" 

// Se for uma expressão (acessar propriedade, fazer conta, chamar função), usamos ${}.
val anos = 4
val descricao = "A palavra tem ${linguagem.length} letras e estudo há ${anos + 1} anos."

// Strings de múltiplas linhas (Raw Strings)
// No JS usamos crases. No Kotlin usamos três aspas duplas (""").
val querySQL = """
    SELECT * FROM usuarios
    WHERE idade > $anos
    ORDER BY nome ASC
""".trimIndent() // O trimIndent() remove os espaços em branco da formatação do código!
```