# Módulo 08: Jetpack Compose (Introdução a UI Declarativa)

**Jetpack Compose** revolucionou a maneira como criamos as Interfaces de Usuário (UIs) no ecossistema Android e Multiplataforma (também aplicável com Desktop/Web em Compose Multiplatform). Se você possui background em **React / TypeScript**, irá encontrar uma base conceitual incrivelmente familiar, pois o Compose atua ativamente sobre o mesmo paradigma declarativo.

No passado (XML), as interfaces eram projetadas mudando *propriedades* através da manipulação do seu estado "imperativamente" no código (equivalente a DOM Manipulation `.innerHTML` com JS Vanilla). O *Jetpack Compose* traz o controle do estado e UI *sob o mesmo domínio*, na base de componentes de forma declarativa.

## Componentes `React.FC` ➡️ `@Composable`

Você já constrói suas telas com **Componentes Baseados em Funções** num arquivo `.tsx`? A equivalência base é exata: o Kotlin adota a anotação `@Composable` sobre funções comuns da linguagem, tornando-as um "componente Renderizável".

### TSX vs Compose

```tsx
// React / TypeScript (Exemplo de Componente)

import React, { useState } from 'react';

type SaudacaoProps = {
    nome: string;
}

export const TituloSaudacao: React.FC<SaudacaoProps> = ({ nome }) => {
    return (
        <div>
            <h1>Olá, {nome}</h1>
            <p>Seja bem vindo(a)!</p>
        </div>
    );
};
```

Em Jetpack Compose (Kotlin), não precisamos mais exportar uma "tag especial" para representar a UI. Em vez disso, funções chamam outras funções composables nativas em uma árvore declarativa.

```kotlin
// Android / Compose
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.foundation.layout.Column

@Composable
fun TituloSaudacao(nome: String) {
    // Column equivaleria à lógica de encapsular os filhos verticais como as Divs no Flexbox `flex-direction: column`
    Column {
        Text(text = "Olá, $nome")
        Text(text = "Seja bem vindo(a)!")
    }
}
```

## Estado (State & Recomposition) == `useState` & Renders

No React, nós usamos o `useState` para criar o estado que a árvore da UI reage ao ser renderizada novamente ("Rerender"). O mecanismo do Compose é extremamente semelhante: é chamado de **Recomposition (Recomposição)**. O estado em Compose é comumente controlado pelo `mutableStateOf`.

Assim como no React usamos `useMemo` para não perder algo caro entre Renderizações, o compose exige que você "se lembre" desse estado através das corrotinas na árvore com o uso de `remember`. Caso contrário, toda execução do composable reiniciaria os valores!

```tsx
// Exemplo Contador React
export const Contador = () => {
    const [conta, setConta] = useState(0);

    return (
        <button onClick={() => setConta(conta + 1)}>
            Você clicou {conta} vezes
        </button>
    );
};
```

Em Compose ficaria:

```kotlin
// Exemplo Contador Jetpack Compose
import androidx.compose.runtime.*
import androidx.compose.material3.Button
import androidx.compose.material3.Text

@Composable
fun Contador() {
    // "by" delegates (delega) o valor armazenado, simulando o comportamento getter/setter
    var conta by remember { mutableStateOf(0) }

    Button(onClick = { conta += 1 }) { // Evento onClick
        Text("Você clicou $conta vezes") // Interpolação String equivalente ao JSX
    }
}
```

## Modificadores (`Modifier`) == Tailwind / CSS e Props

Se você estava habituado com estilizações usando Propriedades CSS Inline e Propriedades (`style={{ padding: 16 }}` ou className="flex-col"), o Compose foca no conceito de Propriedades Compostas via `Modifier`. O Modifier é essencialmente a maneira pela qual você define o preenchimento, as bordas, a coloração, tamanhos fixos e os preenchimentos disponíveis nos Composable functions, construindo padrões visivelmente de cima a baixo em cascata.

```kotlin
import androidx.compose.foundation.layout.padding
import androidx.compose.ui.unit.dp
// Exemplo:
Text(
    text = "Minuto Dev",
    modifier = Modifier.padding(16.dp).fillMaxWidth() 
    // .fillMaxWidth() = width: 100% 
)
```

## Resumo Comparativo: React ➡️ Compose

| Conceito React | Conceito Jetpack Compose |
| ----------------------------- | ----------------------------- |
| `React.FC<Props>` | `@Composable fun Nome(props)` |
| `useState<Type>(valorInicial)`| `remember { mutableStateOf(valorInicial) }` |
| `useEffect` / `useLayoutEffect` | `LaunchedEffect`, `DisposableEffect`, `SideEffect` |
| `<div style={{ flex: 1 }}>` ou `className="flex flex-col"` | `Column` / `Row` / `Box` (Os Layouts Containers) |
| Props como `.onClick` | `onClick = { }` |
| JSX Tree devolvida via Return | Invocação direta das funções filhas sem Return explícito (Side-effects Base Tree). |
