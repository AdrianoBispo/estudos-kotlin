# 🌍 Módulo 09: Múltiplas Plataformas (KMP - Kotlin Multiplatform)

Bem-vindo ao Módulo 09! Aqui vamos explorar o **Kotlin Multiplatform (KMP)**, a solução nativa do Kotlin para compartilhamento de código entre diferentes plataformas (Android, iOS, Web, Desktop e Backend). 

Se você vem do ecossistema JS/TS, provavelmente já conhece abordagens cross-platform como o **React Native**, **Ionic** ou **Electron**. O KMP traz uma filosofia diferente e muito poderosa.

## 🤷‍♂️ KMP vs React Native

Enquanto o React Native tenta unificar tanto a regra de negócios quanto a UI em uma única ponte (Bridge/JSI) usando JavaScript, o **Kotlin Multiplatform** foca primariamente em **compartilhar a lógica de negócios** (Models, ViewModels, requisições de rede, banco de dados).

A UI pode ser escrita de forma nativa para cada plataforma (SwiftUI no iOS, Compose no Android), garantindo performance e acessibilidade nativas sem compromissos. Alternativamente, você pode usar o **Compose Multiplatform** para compartilhar a UI também, em todas as plataformas desktop, web e mobile.

## 🏗️ Estrutura de um Projeto KMP

Em um projeto KMP, você divide seu código em "Source Sets":

- `commonMain`: Código puramente Kotlin compartilhado entre todas as plataformas. Não pode usar bibliotecas específicas de Android (como `android.os.*`) ou iOS.
- `androidMain`: Código específico para Android (tem acesso ao SDK do Android).
- `iosMain`: Código específico para iOS (tem acesso ao Foundation, UIKit, etc).
- E outas como `jsMain`, `jvmMain`, `wasmMain`...

## 🧩 O Padrão `expect` / `actual`

No TypeScript, se precisamos de uma funcionalidade específica da plataforma (Node vs Browser), geralmente fazemos verificações em runtime (`if (typeof window !== 'undefined')`) ou injeção de dependência. No Kotlin, resolvemos isso em tempo de compilação de forma elegante com as palavras-chave `expect` e `actual`.

### Declarando a expectativa (commonMain)
```kotlin
// commonMain/src/.../Platform.kt
expect class Platform() {
    val name: String
}

expect fun getPlatformName(): String
```

### Implementando a realidade (androidMain)
```kotlin
// androidMain/src/.../Platform.kt
actual class Platform actual constructor() {
    actual val name: String = "Android ${android.os.Build.VERSION.SDK_INT}"
}

actual fun getPlatformName(): String = "Android"
```

### Implementando a realidade (iosMain)
```kotlin
// iosMain/src/.../Platform.kt
import platform.UIKit.UIDevice

actual class Platform actual constructor() {
    actual val name: String = UIDevice.currentDevice.systemName() + " " + UIDevice.currentDevice.systemVersion
}

actual fun getPlatformName(): String = "iOS"
```

## 📦 Ecossistema Multiplataforma

O ecossistema Kotlin foi reescrito para suportar KMP. Bibliotecas populares já têm total compatibilidade multiplataforma nativa:
- **Ktor** em vez de Axios/Fetch (Networking REST/GraphQL).
- **Koin** ou **Kodein** (Injeção de Dependências).
- **SQLDelight** ou **Room KMP** (Banco de Dados / SQLite).
- **Kotlinx.coroutines** (Assincronicidade comum e concorrência).
- **Kotlinx.serialization** (Análogo ao `JSON.parse` / `JSON.stringify`).

---
[⬅️ Voltar ao Início](../README.md)
