# 05 - Orientação a Objetos (Kotlin vs TypeScript)

O Kotlin é uma linguagem totalmente orientada a objetos (OO), mas modernizou muito a verbosidade clássica do Java e de outras linguagens mais antigas. Se você vem do TypeScript, vai notar que o Kotlin elimina a palavra-chave `new`, simplifica os construtores, unifica o sistema de tipos (não existem tipos primitivos em minúsculas como `int` ou `boolean`, tudo herda de `Any`) e traz tipos de classes específicos para modelar dados com precisão cirúrgica.

Além disso, o Kotlin mistura o melhor do paradigma Orientado a Objetos com a Programação Funcional, permitindo que escrevas código muito mais expressivo e seguro.

## 1. Classes Regulares, Construtores e Propriedades

No TypeScript, usamos a palavra `class` e definimos explicitamente um método `constructor()`. No Kotlin, o construtor primário faz parte do próprio "cabeçalho" da declaração da classe. E a regra de ouro inicial: **não usamos a palavra `new` para instanciar objetos.**


#### TypeScript:

``` TS
class Pessoa {
    // Declaração de propriedades
    public nome: string;
    private idade: number;

    constructor(nome: string, idade: number) {
        this.nome = nome;
        this.idade = idade;
    }

    getIdade() { return this.idade; }
}

const p = new Pessoa("João", 25);
```

#### Kotlin:

``` kotlin
// Ao colocar 'val' ou 'var' nos parâmetros do construtor, o Kotlin cria as propriedades automaticamente!
// Por padrão, tudo é 'public'. Para ocultar, basta usar 'private'.
class Pessoa(val nome: String, private var idade: Int) {
    fun obterIdade() = idade
}

// Sem a palavra 'new'! Instanciação direta e limpa.
val p = Pessoa("João", 25)
println(p.nome)
```

### O Bloco `init` e Construtores Secundários

Como o construtor primário no Kotlin não tem "chaves" `{}` para colocar lógica de inicialização (como validações complexas ou chamadas a APIs), usamos o bloco `init`. Ele é executado imediatamente após o objeto ser instanciado e as propriedades atribuídas.

``` kotlin
class Produto(val preco: Double) {
    init {
        require(preco > 0) { "O preço deve ser maior que zero!" } // Lança IllegalArgumentException se falso
        println("Produto criado e validado com sucesso. Preço: $preco")
    }
}
```

### Propriedades com Getters e Setters Customizados

No TypeScript, terias de escrever funções `get` e `set`. No Kotlin, o compilador gera os _getters_ e _setters_ invisivelmente por ti. Se precisar de customizar a lógica ao ler ou alterar uma propriedade, pode reescrever o `get()` e `set()` logo abaixo da declaração:

``` kotlin
class Retangulo(var largura: Double, var altura: Double) {
    // Uma propriedade calculada em tempo real (não ocupa memória extra)
    val area: Double
        get() = largura * altura 

    var cor: String = "Branco"
        set(value) {
            println("A mudar a cor de $field para $value")
            field = value // 'field' é a palavra-chave que acede ao valor real armazenado na memória
        }
}
```

### Herança e o modificador `open` (Atenção!)

No TypeScript (e no Java), qualquer classe pode ser herdada por padrão. No Kotlin, a filosofia é _Closed by Default_ (Fechada por Padrão). **Nenhuma classe pode ser herdada a menos que você permita explicitamente usando a palavra `open`.**

``` kotlin
// Se tirares o 'open', a classe Veiculo não pode ter classes filhas.
open class Veiculo(val marca: String) {
    open fun acelerar() = println("A acelerar...")
}

// O ':' simboliza 'extends' ou 'implements'.
class Carro(marca: String) : Veiculo(marca) {
    override fun acelerar() = println("Vrumm! O carro $marca está a acelerar.")
}
```

## 2. Data Classes (O substituto das suas Interfaces/Types do TS)

No TypeScript, quando queres apenas representar um modelo de dados (ex: o payload de uma API ou o estado de um componente), usas uma `interface` ou `type`. No Kotlin, a ferramenta perfeita para isso é a **Data Class**.

Ao colocar a palavra `data` antes da classe, o compilador gera automaticamente para ti nos bastidores:

1. Um método `.equals()` e `.hashCode()` baseados no conteúdo (para comparar valores e não espaços de memória).

2. Um método `.toString()` amigável (imprime `Utilizador(id=1, nome=Maria)` em vez de `Utilizador@5a3b2c`).

3. Um método `.copy()` (o motor da imutabilidade).

``` kotlin
data class Utilizador(val id: Int, val nome: String, val email: String)

val user1 = Utilizador(1, "Maria", "maria@teste.com")
val user2 = Utilizador(1, "Maria", "maria@teste.com")

// 1. O '==' chama o equals() gerado. Compara os valores!
println(user1 == user2) // true (No JS/TS, comparar dois objetos com o mesmo conteúdo retornaria false)

// 2. O método copy() é a versão tipada e segura do Spread Operator '{ ...user1 }' do TS
val userAtualizado = user1.copy(email = "novo_email@teste.com")
// O 'user1' permanece intacto (Imutabilidade garantida).
```

> **Dica de Ouro no Jetpack Compose:** No Compose, a "Recomposição" (o ato de redesenhar o ecrã) só acontece quando o estado muda. O Compose usa o método `.equals()` para saber se o estado mudou. Portanto, você vai usar `data classes` com propriedades `val` (imutáveis) e o método `.copy()` **constantemente** para atualizar o estado da sua UI de forma previsível e otimizada!

## 3. Singletons, Companion Objects e Object Expressions

O Kotlin eliminou a palavra `static` e trouxe formas mais elegantes e orientadas a objetos para lidar com instâncias únicas.

### Declarações de Objeto (`object` para Singletons)

Substitui a palavra `class` por `object` e tem o padrão Singleton implementado de forma segura e _thread-safe_ pelo próprio compilador. O Kotlin garante que só existirá uma instância na memória durante todo o ciclo de vida da aplicação.

```kotlin
object GestorDeSessao {
    var utilizadorAtual: String? = null
    fun iniciarSessao(nome: String) {
        utilizadorAtual = nome
        println("Sessão iniciada para $nome")
    }
}

// Chamas diretamente pelo nome do objeto, sem instanciar!
GestorDeSessao.iniciarSessao("Adriano")
```

### Companion Objects (O substituto do `static`)

Se precisas que métodos ou constantes pertençam à "Classe" em si, e não às instâncias individuais (como no TS com métodos estáticos), deve agrupá-los num `companion object` dentro da classe. A vantagem sobre o TS é que um `companion object` pode herdar classes e implementar interfaces!

``` kotlin
class Fabrica {
    companion object {
        const val VERSAO = "1.0.0"
        fun criarProduto(): String = "Produto Novo"
    }
}

// Chamas diretamente na classe:
val p = Fabrica.criarProduto()
```

### Expressões de Objeto (Anonymous Objects)

No JS/TS pode criar um objeto "on the fly" passando `{ metodo() { ... } }`. No Kotlin, usas `object : InterfaceOuClasse` para criar uma instância anônima.

``` kotlin
// Muito comum ao passar *listeners* no Android clássico
val cliqueListener = object : View.OnClickListener {
    override fun onClick(v: View) {
        println("Fui clicado!")
    }
}
```

## 4. Desestruturação de Propriedades (Cuidado com a Armadilha!)

No JavaScript/TypeScript, a desestruturação (destructuring) é extremamente comum e funciona por nome (`const { idade, nome } = pessoa`). No Kotlin, a desestruturação é nativa em `Data Classes`, mas funciona de forma muito diferente: **ela ocorre por posição.**

#### Kotlin:

``` kotlin
data class Coordenada(val latitude: Double, val longitude: Double)

val posicao = Coordenada(10.5, 20.0)

// Desestruturação! Repare que usamos parênteses () em vez de chaves {}.
val (lat, lng) = posicao // lat recebe 10.5, lng recebe 20.0

// ⚠️ A ARMADILHA PARA DEVS TS:
// Como é por posição, se você tentare trocar os nomes, vai receber os dados errados!
val (longitude, latitude) = posicao 
// 'longitude' receberá 10.5 (porque é o 1º valor na classe) e 'latitude' receberá 20.0.
```

Por que isto acontece? Porque nos bastidores, o Kotlin gera métodos chamados component1(), component2(), e usa-os pela ordem em que declaraste na classe. Tem atenção redobrada nisto!

## 5. Outros Sabores de Classes (Resumo Avançado)

Para modelar o seu domínio de forma robusta, o Kotlin tem ferramentas especializadas:

- **Enum Classes:** Exatamente como no TypeScript, servem para criar um conjunto fechado de valores constantes. No entanto, no Kotlin, os Enums podem ter propriedades e métodos muito mais ricos.

``` kotlin
enum class Status(val codigoApi: Int, val mensagem: String) { 
    PENDENTE(100, "A aguardar"), 
    SUCESSO(200, "Concluído com êxito"), 
    ERRO(500, "Falha no servidor");

    fun imprimir() = println("[$codigoApi] $mensagem")
}
```

- **Sealed Classes / Sealed Interfaces (Os verdadeiros Union Types!):** No TypeScript, pode fazer `type Estado = Carregando | Sucesso | Erro`. O Kotlin resolve isto com **Sealed Classes** (Classes Seladas). Uma classe selada restringe a hierarquia de herança: o compilador sabe exatamente quantas subclasses existem. É a melhor forma de representar estados em arquiteturas Android modernas.

``` kotlin
sealed class EstadoEcra {
    object Carregando : EstadoEcra()
    data class Sucesso(val dados: List<String>) : EstadoEcra()
    data class Erro(val mensagemErro: String) : EstadoEcra()
}

// Quando usas um 'when' com uma classe selada, não precisas do 'else'!
// O compilador obriga-te a tratar todos os cenários possíveis (Exaustividade).
fun renderizar(estado: EstadoEcra) = when(estado) {
    is EstadoEcra.Carregando -> mostrarSpinner()
    is EstadoEcra.Sucesso -> mostrarLista(estado.dados)
    is EstadoEcra.Erro -> mostrarAlerta(estado.mensagemErro)
}
```

- **Value Classes (ou Inline Classes):** São usadas para envolver um tipo primitivo (`String`, `Int`) dando-lhe significado semântico, mas **sem o custo de desempenho** de alocar um novo objeto na memória em tempo de execução. Previne o _Primitive Obsession_ (passar um Int que era um ID num campo que esperava um Int que era a Idade).

``` kotlin
@JvmInline
value class Password(val valor: String)
// O compilador tratará isto como uma String comum para poupar memória, mas 
// o seu código exigirá o tipo 'Password' para manter a segurança e clareza.
```