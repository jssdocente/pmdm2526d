# Gestión de estado en Jetpack Compose

Jetpack Compose es un marco de trabajo moderno para la creación de interfaces de usuario en aplicaciones Android. Con Compose, puedes crear interfaces de usuario de manera declarativa, lo que significa que puedes definir cómo se ve tu aplicación en función del estado de la misma.

## ❓**¿Qué es el estado y por qué es importante ?**

Imagina que construyes una aplicación sin pensar en cómo se gestionan los datos. Al principio, todo parece funcionar. Un botón cambia un texto. Un campo de entrada muestra lo que el usuario escribe. Pero pronto, la complejidad crece:

*   ¿Qué pasa si al girar la pantalla todo el texto que el usuario escribió desaparece?
*   ¿Cómo se asegura una pantalla de que está mostrando los datos más actualizados que acaban de llegar de internet?
*   Si actualizas un dato en la pantalla A, ¿cómo se entera la pantalla B de que debe mostrar ese cambio?

Estos problemas surgen de una **gestión de estado deficiente o inexistente**. La gestión del estado no es un concepto académico y abstracto; es el pilar fundamental sobre el que se construye cualquier aplicación interactiva y fiable.

En esencia, el **estado** es la verdad; es el conjunto de datos que define cómo se ve y se comporta tu aplicación en un momento dado. La **gestión del estado** es la disciplina de controlar cómo y dónde fluye esa verdad a través de tu aplicación.

En Jetpack Compose, un framework de UI **declarativo**, esta importancia se multiplica por diez. A diferencia de los sistemas imperativos (como las Vistas de Android XML) donde tú manualmente buscas un `TextView` y le dices `setText()`, en Compose simplemente declaras: "La UI debe mostrar el valor de *esta* variable de estado". Cuando la variable cambia, la UI **reacciona y se actualiza sola**.

!!! tip "Importante"
    **dominar** la gestión del estado en Compose no es una opción, es el **requisito principal** para construir aplicaciones que funcionen correctamente, sean fáciles de mantener y estén libres de errores impredecibles.

### ¿Qué es el Estado en Jetpack Compose?

En Jetpack Compose, el **estado** es cualquier valor que puede cambiar con el tiempo y que, al hacerlo, debe provocar que la interfaz de usuario se actualice (se redibuje).

Piénsalo de esta manera:

*   El texto que un usuario introduce en un `TextField`.
*   El estado de un `Checkbox` (marcado o no marcado).
*   La posición de un `Slider`.
*   Una lista de mensajes que se carga desde una base de datos.

Todos estos son ejemplos de estado. Si el valor cambia, la UI debe reflejar ese cambio. El mecanismo por el cual Compose redibuja la UI cuando el estado cambia se llama **Recomposición**.

### Declarando el Estado: El Dúo Indispensable `remember` y `mutableStateOf`

Para que Compose pueda "observar" un valor y reaccionar a sus cambios, no podemos usar una variable normal como `var nombre = "Android"`. Necesitamos declararla de una forma especial. Aquí es donde entran en juego dos funciones clave:

1.  `mutableStateOf(valorInicial)`: Esta función toma un valor inicial y lo envuelve en un objeto `State` observable. Cuando el `.value` de este objeto cambia, Compose se entera y programa una recomposición para todas las funciones Composable que lean ese estado.

2.  `remember { ... }`: Las funciones Composable pueden ejecutarse muchas veces (durante las recomposiciones). `remember` es el antídoto contra la "amnesia" de la recomposición. Almacena en caché el resultado del bloque de código que se le pasa, asegurando que este valor **sobreviva** y no se reinicie cada vez que la UI se redibuja.

**La combinación de ambos es la fórmula mágica para el estado local en un Composable:**

`remember { mutableStateOf(valorInicial) }`

*   `mutableStateOf` crea el estado observable.
*   `remember` se asegura de que este estado no se pierda en las recomposiciones.

### Formas de Definir el Estado

Veamos un ejemplo práctico: un simple contador que incrementa un número cada vez que se presiona un botón. Exploraremos las tres formas sintácticas de declarar y usar el estado.

#### Ejemplo 1: La Forma Explícita con `.value`

Esta es la forma más literal. Accedemos y modificamos el valor del estado a través de su propiedad `.value`. Es excelente para entender lo que sucede internamente.

**Analogía:** Tienes una caja (`contadorState`) que es inmutable, pero puedes abrirla para cambiar su contenido (`contadorState.value`).

```kotlin
import androidx.compose.foundation.layout.Column
import androidx.compose.material3.*
import androidx.compose.runtime.*

@Composable
fun ContadorConValue() {
    // Creamos el estado y lo recordamos. Su tipo es MutableState<Int>.
    val contadorState = remember { mutableStateOf(0) }

    Column {
        // Para leer el valor, debemos usar .value
        Text(text = "Has presionado el botón ${contadorState.value} veces.")

        Button(onClick = {
            // Para modificar el valor, también usamos .value
            contadorState.value = contadorState.value + 1
        }) {
            Text("¡Presióname!")
        }
    }
}
```

---

#### Ejemplo 2: La Forma Idiomática con el Delegado `by`

Esta es la forma más común y recomendada en Kotlin. Usamos el delegado de propiedad `by` para que Compose gestione el acceso a `.value` por nosotros. El código resulta mucho más limpio y legible.

**Analogía:** Contratas a un asistente (`by`). En lugar de abrir la caja tú mismo, le pides el valor directamente a tu asistente, y él se encarga de los detalles.

```kotlin
import androidx.compose.foundation.layout.Column
import androidx.compose.material3.*
import androidx.compose.runtime.*

@Composable
fun ContadorConDelegado() {
    // Usamos 'var' porque reasignaremos el valor y 'by' para delegar.
    // 'contador' se comporta ahora como un Int, no como un State<Int>.
    var contador by remember { mutableStateOf(0) }

    Column {
        // Leemos el valor directamente, ¡sin .value!
        Text(text = "Has presionado el botón $contador veces.")

        Button(onClick = {
            // Modificamos el valor directamente.
            contador++ // o contador = contador + 1
        }) {
            Text("¡Presióname!")
        }
    }
}
```
*(Nota: Para usar el delegado `by`, es posible que necesites añadir `import androidx.compose.runtime.getValue` y `setValue`)*

---

#### Ejemplo 3: La Forma con Desestructuración

Esta sintaxis, popular en otros frameworks como React, permite desestructurar el estado en una variable de solo lectura para el valor y una función para actualizarlo.

**Analogía:** Tienes un termostato con dos partes: una pantalla que te muestra la temperatura (`contador`) y una rueda que te permite cambiarla (`setContador`).

```kotlin
import androidx.compose.foundation.layout.Column
import androidx.compose.material3.*
import androidx.compose.runtime.*

@Composable
fun ContadorDesestructurado() {
    // Desestructuramos el State en un valor y una función lambda para actualizarlo.
    val (contador, setContador) = remember { mutableStateOf(0) }

    Column {
        // Leemos el valor directamente.
        Text(text = "Has presionado el botón $contador veces.")

        Button(onClick = {
            // Usamos la función para establecer el nuevo valor.
            setContador(contador + 1)
        }) {
            Text("¡Presióname!")
        }
    }
}
```

!!! bug "Recuerda los puntos clave"

    1.  **El estado es la fuente de verdad** que impulsa tu UI.
    2.  La **recomposición** es el proceso automático por el cual Compose actualiza la UI cuando el estado cambia.
    3.  `mutableStateOf` crea un estado **observable** que Compose puede rastrear.
    4.  `remember` le da **memoria** a tus Composables, permitiendo que el estado sobreviva a las recomposiciones.
    5.  La sintaxis con el delegado **`by` es la forma preferida** por su simplicidad y legibilidad.


!!! info "Video introducción al manejo del estado en Compose"
    <iframe width="560" height="315" src="https://www.youtube.com/embed/R5o1aoUT78o?si=EkLkn1pirJROAgqc" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


## 🔬 **Más en detalle**

Puedes definir el estado de tu aplicación utilizando la función `mutableStateOf()` de Compose.

```kotlin
val contador = mutableStateOf(0)
```

En el ejemplo anterior, se define un estado `contador` con un valor inicial de `0`.

### Observación de estado

Puedes observar el estado de tu aplicación utilizando la función `observeAsState()` de Compose.

```kotlin
val contadorState = contador.observeAsState()
val contador = contadorState.value
```

En el ejemplo anterior, se observa el estado `contador` y se obtiene su valor actual.

### Actualización de estado

Puedes actualizar el estado de tu aplicación utilizando la función `value` de Compose.

```kotlin
contador.value++
```

En el ejemplo anterior, se incrementa en uno el valor del estado `contador`.

!!! danger "¡Importante!"
    El estado en Compose es inmutable, por lo que debes utilizar la función `value` para actualizar el estado.

    Para que haya recomposición, la actualización del estado debe realizarse dentro de un evento de un componente `@Composable`.


#### Ejemplo completo

```kotlin
@Composable
fun Contador() {
    val contador = mutableStateOf(0)
    val contadorState = contador.observeAsState()
    
    Button(onClick = { contador.value++ }) {
        Text(text = "Contador: ${contadorState.value}")
    }
}
```

En el ejemplo anterior, se define un componente `Contador` que muestra un botón y un texto con el valor del estado `contador`. Al hacer clic en el botón, se incrementa en uno el valor del estado `contador`.

### Tipos de estado

En Jetpack Compose, puedes utilizar diferentes tipos de estado para gestionar la información de tu aplicación de forma reactiva.

- `mutableStateOf()`: Crea un estado mutable que puede cambiar a lo largo del tiempo.
- `remember`: Crea un estado que se mantiene entre recomposiciones.
- `derivedStateOf()`: Crea un estado derivado a partir de otros estados.

#### Uso de remember

La función `remember` de Compose te permite crear un estado que se mantiene entre recomposiciones.

```kotlin
@Composable
fun Contador() {
    val contador = remember { mutableStateOf(0) }
    val contadorState = contador.observeAsState()
    
    Button(onClick = { contador.value++ }) {
        Text(text = "Contador: ${contadorState.value}")
    }
}
```

En el ejemplo anterior, se utiliza la función `remember` para crear un estado `contador` que se mantiene entre recomposiciones.

#### Uso de rememberSaveable

La función `rememberSaveable` de Compose te permite crear un estado que se mantiene entre configuraciones.

```kotlin
@Composable
fun Contador() {
    val contador = rememberSaveable { mutableStateOf(0) }
    val contadorState = contador.observeAsState()
    
    Button(onClick = { contador.value++ }) {
        Text(text = "Contador: ${contadorState.value}")
    }
}
```

En el ejemplo anterior, se utiliza la función `rememberSaveable` para crear un estado `contador` que se mantiene entre configuraciones.

!!! tip "Remember vs RememberSaveable"
    La diferencia entre `remember` y `rememberSaveable` es que `rememberSaveable` guarda el estado en el `Bundle` de la actividad para que se pueda restaurar después de una recreación de la actividad.

    Esto es útil para guardar el estado de la aplicación cuando la actividad se destruye y se vuelve a crear, por ejemplo, al girar la pantalla.


#### Uso de derivedStateOf

La función `derivedStateOf` de Compose te permite crear un estado derivado a partir de otros estados.

```kotlin
@Composable
fun Contador() {
    val contador = mutableStateOf(0)
    val doble = derivedStateOf { contador.value * 2 }
    
    Button(onClick = { contador.value++ }) {
        Text(text = "Contador: ${contador.value}, Doble: ${doble.value}")
    }
}
```

En el ejemplo anterior, se utiliza la función `derivedStateOf` para crear un estado `doble` que es el doble del estado `contador`.

## 💦 **Flows en Kotlin**

En Kotlin, un `Flow` es una secuencia de valores que se emiten de forma asíncrona y reactiva. Los `Flow` te permiten trabajar con datos de forma reactiva y gestionar la concurrencia de forma sencilla.

Puedes crear un `Flow` utilizando la función `flowOf()` de Kotlin.

```kotlin
val numeros = flowOf(1, 2, 3, 4, 5)
```

En el ejemplo anterior, se crea un `Flow` `numeros` con los valores `1, 2, 3, 4, 5`.

### Observación de Flows

Puedes observar un `Flow` utilizando la función `collect()` de Kotlin.

```kotlin
numeros.collect { numero ->
    println(numero)
}
```

En el ejemplo anterior, se observa el `Flow` `numeros` y se imprime cada valor que se emite.

### Transformación de Flows

Puedes transformar un `Flow` utilizando operadores como `map`, `filter`, `flatMap`, etc.

```kotlin
val cuadrados = numeros.map { numero -> numero * numero }
val pares = numeros.filter { numero -> numero % 2 == 0 }
```

En el ejemplo anterior, se utilizan los operadores `map` y `filter` para transformar el `Flow` `numeros`.

### Flows en Jetpack Compose

En Jetpack Compose, puedes utilizar Flows para gestionar la información de tu aplicación de forma reactiva.

Puedes convertir un `Flow` en un estado observable utilizando la función `collectAsState()` de Compose.

```kotlin
val numeros = flowOf(1, 2, 3, 4, 5)
val numerosState = numeros.collectAsState()
```

En el ejemplo anterior, se convierte el `Flow` `numeros` en un estado observable `numerosState`.

### Elevación del estado

En Jetpack Compose, puedes elevar el estado de un componente para compartirlo con otros componentes.

Esto te permite gestionar el estado de tu aplicación de forma centralizada y compartirlo entre diferentes partes de tu interfaz de usuario.

```kotlin
@Composable
fun Contador() {
    val contador = remember { mutableStateOf(0) }
    ContadorBoton(contador)
    ContadorTexto(contador)
}

@Composable
fun ContadorBoton(contador: MutableState<Int>) {
    Button(onClick = { contador.value++ }) {
        Text(text = "Incrementar")
    }
}

@Composable
fun ContadorTexto(contador: MutableState<Int>) {
    Text(text = "Contador: ${contador.value}")
}
```

En el ejemplo anterior, se eleva el estado `contador` del componente `Contador` para compartirlo con los componentes `ContadorBoton` y `ContadorTexto`.

De esta forma, el estado `contador` se gestiona de forma centralizada en el componente `Contador` y se comparte con los componentes hijos. Y ambos se recomponen cuando el estado cambia.

Esto también facilita la reutilización de los componentes y la separación de las preocupaciones en tu aplicación. Además de facilitar la prueba y el mantenimiento del código.

!!! info "Elevación del estado vs. Inyección de dependencias"
    - La **elevación del estado** es una técnica común en Jetpack Compose para compartir el estado entre componentes.
    - Otra técnica común es la **inyección de dependencias**, que consiste en pasar el estado como argumento a los componentes que lo necesitan.

    Ambas técnicas tienen sus ventajas y desventajas, y la elección entre ellas depende del diseño y la arquitectura de tu aplicación.

## 📌 Conclusión

La gestión de estado es una parte fundamental de la arquitectura de tu aplicación en Jetpack Compose.

Con Compose, puedes definir, observar y actualizar el estado de tu aplicación de forma reactiva y declarativa.

Los Flows te permiten trabajar con datos de forma reactiva y gestionar la concurrencia de forma sencilla en Kotlin.

#### Ejemplo de gestión del estado básica en una app de Contador

```kotlin
@Composable
fun Contador() {
    val contador = remember { mutableStateOf(0) }
    val contadorState = contador.observeAsState()
    
    Button(onClick = { contador.value++ }) {
        Text(text = "Contador: ${contadorState.value}")
    }
}
```

En el ejemplo anterior, se define un componente `Contador` que muestra un botón y un texto con el valor del estado `contador`. Al hacer clic en el botón, se incrementa en uno el valor del estado `contador`.


## 📦 Recursos

- [Documentación oficial de Jetpack Compose](https://developer.android.com/jetpack/compose?hl=es-419): La documentación oficial de Jetpack Compose, que incluye guías, tutoriales y ejemplos para aprender a usar Compose.
- [Ejemplos básicos de Compose](https://github.com/resuadam2/TutorialCompose): Un repositorio con ejemplos básicos de Jetpack Compose para que puedas aprender a crear interfaces de usuario con Compose.   
- [Avanzando con Kotlin y el manejo de la UI - Codelabs](https://developer.android.com/courses/android-basics-compose/unit-2?hl=es-419)
- [Más Kotlin y listas de elementos (LazyColumn) - Codelabs](https://developer.android.com/courses/android-basics-compose/unit-3?hl=es-419)