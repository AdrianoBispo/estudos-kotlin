# Kotlin Tutorial

![Kotlin tutorial](https://media.geeksforgeeks.org/wp-content/cdn-uploads/20230305134943/Kotlin-Tutorial.png)

Este tutorial Kotlin foi desenvolvido tanto para iniciantes quanto para profissionais, abrangendo conceitos básicos e avançados da linguagem de programação Kotlin. Neste tutorial do Kotlin, você aprenderá vários tópicos importantes do Kotlin, incluindo tipos de dados, fluxo de controle, funções, programação orientada a objetos, coleções e muito mais. Também nos aprofundaremos em conceitos avançados, como corrotinas Kotlin, segurança nula e programação funcional em Kotlin.

## Pré-requisitos

Para começar a usar Kotlin, é útil ter uma base em várias áreas-chave. Aqui estão os principais pré-requisitos para iniciantes:

-   ****Conhecimento básico de Java:**** Kotlin foi projetado para ser compatível com Java, portanto, ter um conhecimento básico da [linguagem de programação Java](https://www.geeksforgeeks.org/java/java/) é recomendado.
-   ****Ambiente de programação:**** Pressupõe-se familiaridade com qualquer ambiente de programação.
-   ****Conceitos básicos**** : Pressupõe-se o conhecimento de conceitos básicos como variáveis, comandos, sintaxe, etc.
-   ****Conhecimento básico de IDEs:**** Familiaridade com Ambientes de Desenvolvimento Integrado (IDEs), como o IntelliJ IDEA ou o Android Studio, ajudará você a navegar e usar essas ferramentas com eficácia.

## O que é Kotlin?

Kotlin é uma linguagem de programação moderna criada pela JetBrains em 2011, a mesma empresa por trás do popular IntelliJ IDEA. Ela roda na [Máquina Virtual Java](https://www.geeksforgeeks.org/java/java-virtual-machine-jvm-stack-area/) (JVM) e também pode ser compilada para JavaScript ou código nativo. Kotlin é uma linguagem orientada a objetos e considerada uma linguagem "melhor" que Java, mantendo, ainda assim, total interoperabilidade com código Java. Kotlin é patrocinada pelo Google e foi anunciada como uma das linguagens oficiais para desenvolvimento Android em 2017.

## Por que usar Kotlin no desenvolvimento para Android?

-   ****Suporte do Google:**** O Kotlin tem suporte oficial do Google para desenvolvimento Android, garantindo sua boa compatibilidade com o Android Studio.
-   ****Fácil de ler e escrever**** : o código Kotlin é mais curto e claro do que o Java, tornando-o mais fácil de entender e trabalhar.
-   ****Compatível com Java:**** Kotlin pode ser usado com Java no mesmo projeto, facilitando a transição de Java para Kotlin.
-   ****Segurança contra valores nulos:**** Kotlin ajuda a evitar erros ao lidar claramente com valores nulos, que são uma fonte comum de bugs em Java.
-   ****Código assíncrono aprimorado:**** as corrotinas do Kotlin facilitam o gerenciamento de tarefas em segundo plano, como solicitações de rede, sem bloquear o aplicativo.
-   ****Comunidade forte:**** Kotlin possui um número crescente de bibliotecas, ferramentas e recursos da comunidade para dar suporte aos desenvolvedores.

## 📚 Índice de Módulos

O conteúdo prático está dividido em diretórios de módulos. Acesse os links abaixo para ler sobre cada tema:

- [**Módulo 01: Fundamentos Básicos**](./modulo-01/)
  *Variáveis, Imutabilidade (`val` vs `var`) e os primeiros passos na linguagem Kotlin.*
- [**Módulo 02: Controle de Fluxo**](./modulo-02/)
  *Estruturas condicionais modernas, o poder do `when` em de switch, e o uso de `if` como expressão de retorno.*
- [**Módulo 03: Null Safety**](./modulo-03/)
  *Como o Kotlin lida com valores opcionais de forma segura, resolvendo o infame "Erro de um bilhão de dólares" (NullPointerException).*
- [**Módulo 04: Funções**](./modulo-04/)
  *Funções clássicas, Lambdas, Funções de Extensão (Extension Functions), parâmetros nomeados e o uso de `Unit` no lugar de `void/undefined`.*
- [**Módulo 05: Orientação a Objetos**](./modulo-05/)
  *Classes fundamentais, data classes, inicialização e construtores primários que dispensam o uso da palavra `new`.*
- [**Módulo 06: Coleções, Type Aliases e Generics**](./modulo-06/)
  *Diferença rigorosa entre listas mutáveis (MutableList) e imutáveis (List), uso de Type Aliases e manipulação com Generics.*
- [**Módulo 07: Corrotinas (Coroutines)**](./modulo-07/)
  *Programação assíncrona com `suspend` functions e frameworks nativos comparando com Promises e `async/await` do TypeScript.*
- [**Módulo 08: Jetpack Compose**](./modulo-08/)
  *Introdução a UI Declarativa e similaridades com os componentes e renderizações de estado do React.*
- [**Módulo 09: Múltiplas Plataformas (KMP)**](./modulo-09/)
  *Explorando o Kotlin Multiplatform para compartilhamento de código e regras de negócios entre plataformas usando `expect` e `actual`.*
- [**Módulo 10: Testes (Unitários e Instrumentados)**](./modulo-10/)
  *Um comparativo prático de bibliotecas e fluxos de teste usando de JUnit, MockK e Compose Testing para exaustivos testes equivalentes a Jest, Vitest e React Testing Library.*

## 🛠️ Como utilizar o material do repositório

Leia as anotações nos arquivos `README.md` que estão dentro de cada um dos módulos. Para praticar o código fornecido, grande parte dos conceitos abordados podem ser testados diretamente através do [**Kotlin Playground**](https://play.kotlinlang.org/) no seu navegador, sem necessidade de configuração prévia, ou ainda utilizando IDEs como *IntelliJ IDEA* ou *Android Studio*.
