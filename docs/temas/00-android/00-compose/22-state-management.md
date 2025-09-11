import CodeBlock from '@theme/CodeBlock';

# Gestión de estado en Jetpack Compose

Jetpack Compose es un marco de trabajo moderno para la creación de interfaces de usuario en aplicaciones Android. Con Compose, puedes crear interfaces de usuario de manera declarativa, lo que significa que puedes definir cómo se ve tu aplicación en función del estado de la misma.

## Estado en Jetpack Compose

En Jetpack Compose, el estado es una parte fundamental de la arquitectura de tu aplicación. 

El estado representa la información que puede cambiar a lo largo del tiempo y que afecta a la apariencia y el comportamiento de tu interfaz de usuario. 

Puedes definir y observar el estado de tu aplicación de forma reactiva en Compose.

:::info[Video introducción al manejo del estado en Compose]
<iframe width="560" height="315" src="https://www.youtube.com/embed/R5o1aoUT78o?si=EkLkn1pirJROAgqc" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
:::

## Definición de estado

Puedes definir el estado de tu aplicación utilizando la función `mutableStateOf()` de Compose.

```kotlin
val contador = mutableStateOf(0)
```

En el ejemplo anterior, se define un estado `contador` con un valor inicial de `0`.

## Observación de estado

Puedes observar el estado de tu aplicación utilizando la función `observeAsState()` de Compose.

```kotlin
val contadorState = contador.observeAsState()
val contador = contadorState.value
```

En el ejemplo anterior, se observa el estado `contador` y se obtiene su valor actual.

## Actualización de estado

Puedes actualizar el estado de tu aplicación utilizando la función `value` de Compose.

```kotlin
contador.value++
```

En el ejemplo anterior, se incrementa en uno el valor del estado `contador`.

:::danger[¡Importante!]
El estado en Compose es inmutable, por lo que debes utilizar la función `value` para actualizar el estado.

Para que haya recomposición, la actualización del estado debe realizarse dentro de un evento de un componente `@Composable`.
:::

## Ejemplo completo

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

## Tipos de estado

En Jetpack Compose, puedes utilizar diferentes tipos de estado para gestionar la información de tu aplicación de forma reactiva.

- `mutableStateOf()`: Crea un estado mutable que puede cambiar a lo largo del tiempo.
- `remember`: Crea un estado que se mantiene entre recomposiciones.
- `derivedStateOf()`: Crea un estado derivado a partir de otros estados.

## Uso de remember

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

## Uso de rememberSaveable

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

:::tip[Remember vs RememberSaveable]
La diferencia entre `remember` y `rememberSaveable` es que `rememberSaveable` guarda el estado en el `Bundle` de la actividad para que se pueda restaurar después de una recreación de la actividad.

Esto es útil para guardar el estado de la aplicación cuando la actividad se destruye y se vuelve a crear, por ejemplo, al girar la pantalla.
:::

## Uso de derivedStateOf

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

## Flows en Kotlin

En Kotlin, un `Flow` es una secuencia de valores que se emiten de forma asíncrona y reactiva. Los `Flow` te permiten trabajar con datos de forma reactiva y gestionar la concurrencia de forma sencilla.

Puedes crear un `Flow` utilizando la función `flowOf()` de Kotlin.

```kotlin
val numeros = flowOf(1, 2, 3, 4, 5)
```

En el ejemplo anterior, se crea un `Flow` `numeros` con los valores `1, 2, 3, 4, 5`.

## Observación de Flows

Puedes observar un `Flow` utilizando la función `collect()` de Kotlin.

```kotlin
numeros.collect { numero ->
    println(numero)
}
```

En el ejemplo anterior, se observa el `Flow` `numeros` y se imprime cada valor que se emite.

## Transformación de Flows

Puedes transformar un `Flow` utilizando operadores como `map`, `filter`, `flatMap`, etc.

```kotlin
val cuadrados = numeros.map { numero -> numero * numero }
val pares = numeros.filter { numero -> numero % 2 == 0 }
```

En el ejemplo anterior, se utilizan los operadores `map` y `filter` para transformar el `Flow` `numeros`.

## Flows en Jetpack Compose

En Jetpack Compose, puedes utilizar Flows para gestionar la información de tu aplicación de forma reactiva.

Puedes convertir un `Flow` en un estado observable utilizando la función `collectAsState()` de Compose.

```kotlin
val numeros = flowOf(1, 2, 3, 4, 5)
val numerosState = numeros.collectAsState()
```

En el ejemplo anterior, se convierte el `Flow` `numeros` en un estado observable `numerosState`.

## Elevación del estado

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

:::info[Elevación del estado vs. Inyección de dependencias]
La elevación del estado es una técnica común en Jetpack Compose para compartir el estado entre componentes.

Otra técnica común es la inyección de dependencias, que consiste en pasar el estado como argumento a los componentes que lo necesitan.

Ambas técnicas tienen sus ventajas y desventajas, y la elección entre ellas depende del diseño y la arquitectura de tu aplicación.
:::

## Conclusión

La gestión de estado es una parte fundamental de la arquitectura de tu aplicación en Jetpack Compose.

Con Compose, puedes definir, observar y actualizar el estado de tu aplicación de forma reactiva y declarativa.

Los Flows te permiten trabajar con datos de forma reactiva y gestionar la concurrencia de forma sencilla en Kotlin.

## Ejemplo de gestión del estado básica en una app de Contador

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


## Recursos

- [Documentación oficial de Jetpack Compose](https://developer.android.com/jetpack/compose?hl=es-419): La documentación oficial de Jetpack Compose, que incluye guías, tutoriales y ejemplos para aprender a usar Compose.

- [Ejemplos básicos de Compose](https://github.com/resuadam2/TutorialCompose): Un repositorio con ejemplos básicos de Jetpack Compose para que puedas aprender a crear interfaces de usuario con Compose.   

- [Avanzando con Kotlin y el manejo de la UI - Codelabs](https://developer.android.com/courses/android-basics-compose/unit-2?hl=es-419)

- [Más Kotlin y listas de elementos (LazyColumn) - Codelabs](https://developer.android.com/courses/android-basics-compose/unit-3?hl=es-419)