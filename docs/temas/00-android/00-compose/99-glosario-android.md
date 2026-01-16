# Glosario de Android

Este documento sirve como referencia rápida para los conceptos clave del desarrollo moderno de Android. Los términos están agrupados por áreas temáticas para facilitar su comprensión en contexto.

---

## 1. Fundamentos de Kotlin (El Lenguaje)

Conceptos del lenguaje sobre los que se construye todo el desarrollo moderno.

### **Data Class**
Una clase diseñada específicamente para contener datos. El compilador genera automáticamente métodos útiles como `toString()`, `equals()`, `hashCode()` y `copy()`. Fundamental para definir modelos y estados de UI.
```kotlin
data class Usuario(val id: String, val nombre: String)
```

### **Sealed Class / Interface**
Una jerarquía de clases restringida. Define un conjunto cerrado de posibles subclases. Es ideal para modelar **Estados de UI** (ej: `Cargando`, `Exito`, `Error`) porque obliga al compilador a manejar todos los casos posibles en un `when`.

### **Lambda**
Una función anónima que puede ser tratada como un valor (pasada como parámetro o guardada en una variable). Es el motor de Jetpack Compose (ej: `onClick = { ... }`).
```kotlin
val suma = { x: Int, y: Int -> x + y }
```

### **Extension Function**
Capacidad de Kotlin para añadir funciones a una clase existente sin tener que heredar de ella.
```kotlin
fun String.esEmailValido(): Boolean { ... }
"test@test.com".esEmailValido()
```

### **Higher-Order Function (Función de Orden Superior)**
Una función que acepta otra función como parámetro o devuelve una función. Es la base de los `LazyColumn`, `Button`, y casi cualquier Composable contenedor.

---

## 2. Android Core (El Sistema)

Los cimientos del sistema operativo Android que todo desarrollador debe conocer.

### **Activity**
Un componente fundamental que representa una pantalla con una interfaz de usuario. Es el punto de entrada principal para la interacción del usuario. En Compose, solemos tener una sola Activity (`MainActivity`) que contiene toda la app ("Single Activity Architecture").

### **Context**
Es el puente entre tu código y el sistema Android. Permite acceder a recursos (cadenas, colores), iniciar Activities, enviar Broadcasts y acceder a servicios del sistema.

### **Manifest (`AndroidManifest.xml`)**
El "DNI" de la aplicación. Un archivo XML que describe la app al sistema: nombre, icono, componentes (Activities, Services), y **permisos** requeridos.

### **Intent**
Un objeto de mensajería que se usa para solicitar una acción a otro componente de la app.
*   **Intents Explícitos**: Para iniciar una Activity específica dentro de tu app.
*   **Intents Implícitos**: Para declarar una intención general (ej: "Quiero abrir una URL") y dejar que el sistema decida qué app usar (Chrome, Firefox, etc.).

### **Lifecycle (Ciclo de Vida)**
La serie de estados por los que pasa una Activity o Fragment (Creado, Iniciado, Reanudado, Pausado, Destruido). Entenderlo es vital para evitar fugas de memoria y guardar datos correctamente.

---

## 3. Jetpack Compose (La UI Moderna)

El kit de herramientas declarativo para construir interfaces nativas.

### **Composable (`@Composable`)**
Una función de Kotlin anotada con `@Composable` que describe **QUÉ** UI se debe mostrar (no CÓMO). Es el bloque de construcción básico de Compose.

### **State (`MutableState<T>`)**
Un contenedor de datos observable. Cuando el valor dentro de un `State` cambia, Compose detecta el cambio y programa una **Recomposición**.

### **Recomposition (Recomposición)**
El proceso mediante el cual Compose re-ejecuta tus funciones composables para actualizar la UI con nuevos datos. Compose es inteligente y solo redibuja las partes que han cambiado.

### **Modifier**
Un objeto que se encadena para decorar o configurar un composable. Controla el tamaño, padding, clics, fondo, bordes, etc. El orden de los modificadores importa.
```kotlin
Box(modifier = Modifier.size(50.dp).background(Color.Red))
```

### **Side Effect (Efecto Secundario)**
Cualquier cambio de estado que ocurra fuera del alcance de una función composable (ej: llamadas a red, acceso a BBDD, logs). En Compose, deben controlarse con APIs especiales como `LaunchedEffect` para evitar que se ejecuten infinitas veces durante las recomposiciones.

### **Scaffold**
Un composable que implementa la estructura visual básica de Material Design. Proporciona "slots" (huecos) predefinidos para la `TopBar`, `BottomBar`, `FloatingActionButton` y el contenido principal.

---

## 4. Arquitectura y Concurrencia (El "Cerebro")

Cómo estructurar la aplicación y manejar tareas en segundo plano.

### **MVVM (Model-View-ViewModel)**
El patrón de arquitectura recomendado por Google.
*   **Model**: Datos y lógica de negocio.
*   **View**: La UI (Composable).
*   **ViewModel**: Intermediario que guarda el estado de la UI y gestiona la lógica, sobreviviendo a cambios de configuración (como rotar la pantalla).

### **UDF (Unidirectional Data Flow)**
Flujo de Datos Unidireccional. Un patrón donde:
1.  El **Estado** fluye hacia abajo (del ViewModel a la UI).
2.  Los **Eventos** fluyen hacia arriba (de la UI al ViewModel).
Esto hace que el estado sea predecible y fácil de depurar.

### **Clean Architecture**
Una forma de organizar el código en capas (Presentación, Dominio, Datos) para que sea mantenible, testeable e independiente de librerías externas. La capa de **Dominio** (Use Cases) es el núcleo puro de la lógica de negocio.

### **Corrutina (Coroutine)**
Un hilo ligero gestionado por Kotlin. Permite escribir código asíncrono (como llamadas a red) de forma secuencial y legible, sin bloquear el hilo principal (UI Thread).

### **Suspend Function**
Una función que puede ser pausada y reanudada. Solo puede ser llamada desde una corrutina u otra función suspendida. Es la base de la asincronía en Kotlin.

### **Flow / StateFlow**
Mecanismos para manejar flujos de datos asíncronos (streams).
*   **Flow (Cold)**: Flujo de datos que solo emite cuando hay un colector activo.
*   **StateFlow (Hot)**: Un tipo especial de Flow que siempre tiene un valor actual y emite actualizaciones a sus colectores. Es el reemplazo moderno de `LiveData` en arquitectura Kotlin pura.
