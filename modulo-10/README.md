# 🧪 Módulo 10: Testes (Unitários e Instrumentados)

Bem-vindo ao Módulo 10! Testes são fundamentais em qualquer projeto moderno e não poderiam ficar de fora. Neste módulo, vamos fazer paralelos com ferramentas populares do ecossistema JS/TS, como o **Jest**, **Vitest** e **React Testing Library**.

No mundo Android/Kotlin, os testes são frequentemente separados em duas categorias principais:
1. **Testes Unitários Locais**: Rodam diretamente na JVM local de desenvolvimento (Seu PC, Mac e CI). São extremamente rápidos, totalmente análogos aos testes do Jest tradicionais rodando no tempo de execução do Node.
2. **Testes Instrumentados**: Rodam em um emulador ou dispositivo físico com Android O.S real. Necessários quando usamos APIs diretas do sistema do aparelho como o Contexto, ou testes visuais ponta-a-ponta. Análogos ao uso do Cypress ou Playwright.

## 🛠️ Ferramentas Equivalentes

| Ecossistema JS/TS (Jest/RTL) | Ecossistema Kotlin/Android |
| --- | --- |
| Jest / Vitest (Runner) | **JUnit 4** ou **JUnit 5** |
| Jest Mocks (`jest.fn()`, automocking) | **MockK** ou **Mockito** |
| Asserções avançadas (`expect(x).toBe`) | **Truth**, **Striker** ou Asserções embutidas |
| React Testing Library | **Compose Testing** (`createComposeRule`) |
| Testes Async / Data Streams | **Turbine** (para testar Flows do Kotlin) |

## 📝 Escrevendo Testes Unitários

### Exemplo em TypeScript (Vitest/Jest)
```typescript
describe("Calculadora", () => {
  it("deve somar dois numeros corretamente", () => {
    const calc = new Calculadora();
    expect(calc.somar(2, 3)).toBe(5);
  });
});
```

### Exemplo em Kotlin (JUnit + Truth)
```kotlin
import org.junit.Test
import com.google.common.truth.Truth.assertThat

class CalculadoraTest {

    @Test
    fun `deve somar dois numeros corretamente`() {
        // Arrange
        val calc = Calculadora()
        
        // Act
        val resultado = calc.somar(2, 3)
        
        // Assert
        assertThat(resultado).isEqualTo(5)
    }
}
```
*Dica de Ouro: Pelo uso de \`crases\` ao redor do nome das funções, podemos criar os de modo super descritivas sem o famoso `get_number_does_x` semelhante à flexibilidade de strings do Jest!*

## 🎭 Mocks e Stubs com MockK

Se você usava o Jest para "mockar" ou espionar serviços, no Kotlin o padrão amigável moderno e robusto é o incrível **MockK**.

```kotlin
class UserServiceTest {
    
    // Criação do mock (spy/mock)
    val userRepository = mockk<UserRepository>()
    val userService = UserService(userRepository)

    @Test
    fun `deve retornar o nome em maiusculo`() {
        // Mockando o valor que será devolvido pelo repositório - (análogo ao mockReturnValue)
        every { userRepository.getUserName(1) } returns "joão"

        val resultado = userService.getUserNameUpper(1)

        assertThat(resultado).isEqualTo("JOÃO")
        
        // Verificando de modo estrito se e quantas vezes a função foi chamada - (análogo ao toHaveBeenCalledTimes(1))
        verify(exactly = 1) { userRepository.getUserName(1) }
    }
}
```

## 📱 Testes de Interface (UI) no Jetpack Compose

O Jetpack Compose trouxe o pacote oficial chamado Compose Testing que é perfeitamente assemelhado pelo React Testing Library. Você confia em interações baseadas na semântica real em vez de varrer a árvore de Views na memória:

```kotlin
import androidx.compose.ui.test.junit4.createComposeRule
import androidx.compose.ui.test.onNodeWithText
import androidx.compose.ui.test.performClick
import androidx.compose.ui.test.assertIsDisplayed
import org.junit.Rule
import org.junit.Test

class CounterScreenTest {

    @get:Rule
    val composeTestRule = createComposeRule()

    @Test
    fun `deve incrementar o mostrador textual em tela ao clicar no botao`() {
        // "Renderiza/Monta" o componente unicamente
        composeTestRule.setContent {
            CounterScreen() // componente @Composable a ser testado independente
        }

        // Verifica estado inicial de exibição de texto
        composeTestRule.onNodeWithText("Cliques: 0").assertIsDisplayed()

        // Executa uma inteiração de evento do usuário baseada em uma String 
        composeTestRule.onNodeWithText("Incrementar").performClick()

        // Valida que a ação funcionou perfeitamente após composição de estados em tela com Reactivity do Jetpack 
        composeTestRule.onNodeWithText("Cliques: 1").assertIsDisplayed()
    }
}
```

---
[⬅️ Voltar ao Início](../README.md)
