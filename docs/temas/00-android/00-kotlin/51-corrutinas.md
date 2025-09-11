# Corrutinas en Kotlin

Las corrutinas son una característica de Kotlin que permite escribir código asíncrono de manera más sencilla y legible. Las corrutinas son funciones que pueden suspenderse y reanudarse en un punto determinado, lo que permite realizar operaciones asíncronas sin bloquear el hilo principal.    

```kotlin
suspend fun main() {
    val resultado = async { obtenerDatos() }
    println("Datos: ${resultado.await()}")
}
suspend fun obtenerDatos(): String {
    delay(1000)
    return "Datos obtenidos"
}
```

En el ejemplo anterior, se define una función `obtenerDatos` que simula una operación asíncrona que tarda 1 segundo en completarse. La función `main` utiliza la función `async` para ejecutar la función `obtenerDatos` en una corrutina y espera a que se complete utilizando la función `await`. 

## Código síncrono

El código síncrono es el código que se ejecuta de manera secuencial, es decir, una instrucción se ejecuta después de que la anterior haya terminado. En Kotlin, el código síncrono se ejecuta en el hilo principal de la aplicación.    

```kotlin
fun main() {
    val resultado = obtenerDatos()
    println("Datos: $resultado")
}
fun obtenerDatos(): String {
    Thread.sleep(1000)
    return "Datos obtenidos"
}
```

En el ejemplo anterior, se define una función `obtenerDatos` que simula una operación síncrona que tarda 1 segundo en completarse. La función `main` llama a la función `obtenerDatos` y espera a que se complete antes de imprimir los datos obtenidos.

## Código asíncrono

El código asíncrono es el código que se ejecuta de manera concurrente, es decir, varias instrucciones se pueden ejecutar al mismo tiempo. En Kotlin, el código asíncrono se puede escribir utilizando corrutinas.    

```kotlin
suspend fun main() {
    val resultado = async { obtenerDatos() }
    println("Datos: ${resultado.await()}")
}

suspend fun obtenerDatos(): String {
    delay(1000)
    return "Datos obtenidos"
}
```

En el ejemplo anterior, se define una función `obtenerDatos` que simula una operación asíncrona que tarda 1 segundo en completarse. La función `main` utiliza la función `async` para ejecutar la función `obtenerDatos` en una corrutina y espera a que se complete utilizando la función `await`.

## Los métodos launch y async

En Kotlin, las funciones `launch` y `async` se utilizan para crear corrutinas. 

La principal diferencia entre ellas es que `launch` se utiliza para ejecutar una corrutina que no devuelve un valor, mientras que `async` se utiliza para ejecutar una corrutina que devuelve un valor.  

```kotlin
suspend fun main() {
    val resultado = async { obtenerDatos() }
    println("Datos: ${resultado.await()}")
}

suspend fun obtenerDatos(): String {
    delay(1000)
    return "Datos obtenidos"
}

suspend fun main() {
    launch { obtenerDatos() }
    println("Datos obtenidos")
}

suspend fun obtenerDatos() {
    delay(1000)
    println("Datos obtenidos")
}
```

En el ejemplo anterior, se define una función `obtenerDatos` que simula una operación asíncrona que tarda 1 segundo en completarse. La función `main` utiliza la función `async` para ejecutar la función `obtenerDatos` en una corrutina y espera a que se complete utilizando la función `await`. En el segundo ejemplo, se utiliza la función `launch` para ejecutar la función `obtenerDatos` en una corrutina sin esperar a que se complete.   

## Excepciones

En Kotlin, las corrutinas pueden lanzar excepciones que se pueden manejar utilizando bloques `try-catch`. Las excepciones lanzadas en una corrutina se propagan a la corrutina padre y se pueden manejar en el hilo principal.  

```kotlin
suspend fun main() {
    try {
        val resultado = async { obtenerDatos() }
        println("Datos: ${resultado.await()}")
    } catch (e: Exception) {
        println("Error: ${e.message}")
    }
}

suspend fun obtenerDatos(): String {
    delay(1000)
    throw Exception("Error al obtener los datos")
    return "Datos obtenidos"
}
```

En el ejemplo anterior, se define una función `obtenerDatos` que simula una operación asíncrona que tarda 1 segundo en completarse y lanza una excepción. La función `main` utiliza un bloque `try-catch` para manejar la excepción lanzada en la corrutina.

    - [Documentación oficial de Kotlin](https://kotlinlang.org/docs/coroutines-guide.html): La documentación oficial de Kotlin sobre corrutinas, que incluye guías, tutoriales y ejemplos para aprender a usar corrutinas en Kotlin.

:::warning[La importancia de controlar las excepciones al usar corrutinas]
Cuando se utilizan corrutinas en Kotlin, es importante controlar las excepciones que se pueden producir durante la ejecución de la aplicación. Las excepciones no controladas pueden provocar fallos en la aplicación y afectar negativamente la experiencia del usuario.   

Por lo tanto, es importante utilizar bloques `try-catch` para manejar las excepciones que se pueden producir en las corrutinas. Además, es recomendable utilizar la función `CoroutineExceptionHandler` para manejar las excepciones que se producen en las corrutinas de manera global.

```kotlin
val exceptionHandler = CoroutineExceptionHandler { _, exception ->
    println("Error: ${exception.message}")
}

suspend fun main() {
    try {
        val resultado = async { obtenerDatos() }
        println("Datos: ${resultado.await()}")
    } catch (e: Exception) {
        println("Error: ${e.message}")
    }
}   

suspend fun obtenerDatos(): String {
    delay(1000)
    throw Exception("Error al obtener los datos")
    return "Datos obtenidos"
}
```

:::

## Cancelación

En Kotlin, las corrutinas se pueden cancelar utilizando la función `cancel()`. La cancelación de una corrutina no detiene inmediatamente su ejecución, sino que la marca como cancelada y permite que se complete de manera segura. 

```kotlin
suspend fun main() {
    val job = launch {
        try {
            obtenerDatos()
        } catch (e: CancellationException) {
            println("Corrutina cancelada")
        }
    }
    delay(500)
    job.cancel()
}

suspend fun obtenerDatos() {
    delay(1000)
    println("Datos obtenidos")
}
```

En el ejemplo anterior, se define una función `obtenerDatos` que simula una operación asíncrona que tarda 1 segundo en completarse. La función `main` utiliza la función `launch` para ejecutar la función `obtenerDatos` en una corrutina y espera 500 milisegundos antes de cancelar la corrutina utilizando la función `cancel()`. Si la corrutina es cancelada, se lanza una excepción `CancellationException` que se puede manejar en el bloque `try-catch`.   

## La clase Job

En Kotlin, la clase `Job` se utiliza para gestionar la ejecución de corrutinas. Un `Job` representa una unidad de trabajo que se puede cancelar y supervisar. 

```kotlin
suspend fun main() {
    val job = launch {
        obtenerDatos()
    }
    delay(500)
    job.cancel()
}
suspend fun obtenerDatos() {
    delay(1000)
    println("Datos obtenidos")
}
```

En el ejemplo anterior, se define una función `obtenerDatos` que simula una operación asíncrona que tarda 1 segundo en completarse. La función `main` utiliza la función `launch` para ejecutar la función `obtenerDatos` en una corrutina y espera 500 milisegundos antes de cancelar la corrutina utilizando la función `cancel()`. 

El objeto `Job` se utiliza para gestionar la ejecución de la corrutina y se puede utilizar para supervisar su estado. 

:::info[La importancia de Job]
Job desempeña un papel importante para garantizar la simultaneidad estructurada, ya que administra el ciclo de vida de las corrutinas y mantiene la relación de superior y secundario.
:::

### Jerarquía de trabajos

En Kotlin, la jerarquía de trabajos se utiliza para gestionar la ejecución de corrutinas. Un trabajo (Job) representa una unidad de trabajo que se puede cancelar y supervisar. Los trabajos se pueden organizar en una jerarquía para gestionar la ejecución de corrutinas de manera más eficiente. 

```kotlin
suspend fun main() {
    val parentJob = Job()
    val childJob1 = Job(parentJob)
    val childJob2 = Job(parentJob)

    launch(childJob1) {
        obtenerDatos()
    }
    launch(childJob2) {
        obtenerDatos()
    }

    delay(500)
    parentJob.cancel()
}

suspend fun obtenerDatos() {
    delay(1000)
    println("Datos obtenidos")
}
```

En el ejemplo anterior, se define una función `obtenerDatos` que simula una operación asíncrona que tarda 1 segundo en completarse. La función `main` crea un trabajo padre y dos trabajos secundarios que se ejecutan en paralelo. Después de 500 milisegundos, se cancela el trabajo padre utilizando la función `cancel()`, lo que cancela todos los trabajos secundarios.

![Jerarquía de trabajos](https://developer.android.com/static/codelabs/basic-android-kotlin-compose-coroutines-kotlin-playground/img/d6f120976b283e0_960.png?hl=es-419)

## CoroutineScope

En Kotlin, el `CoroutineScope` es una interfaz que define un ámbito para las corrutinas. 

Un `CoroutineScope` se utiliza para crear y gestionar corrutinas en una aplicación. 

```kotlin
class MyCoroutineScope : CoroutineScope {
    private val job = Job()
    override val coroutineContext: CoroutineContext
        get() = Dispatchers.Main + job

    fun cancel() {
        job.cancel()
    }
suspend fun main() {
    val scope = MyCoroutineScope()
    scope.launch {
        obtenerDatos()
    }
    delay(500)
    scope.cancel()
}

suspend fun obtenerDatos() {
    delay(1000)
    println("Datos obtenidos")
}

}
```

En el ejemplo anterior, se define una clase `MyCoroutineScope` que implementa la interfaz `CoroutineScope`. La clase `MyCoroutineScope` define un ámbito para las corrutinas utilizando la propiedad `coroutineContext`, que combina el despachador `Dispatchers.Main` y un objeto `Job`. La clase también incluye una función `cancel()` que cancela todas las corrutinas en el ámbito. En la función `main`, se crea una instancia de `MyCoroutineScope` y se utiliza para ejecutar una corrutina que llama a la función `obtenerDatos`. Después de 500 milisegundos, se cancela el ámbito utilizando la función `cancel()`.  

:::tip[launch y async en CoroutineScope]
launch() y async() son funciones de extension de la interfaz `CoroutineScope` que se utilizan para crear corrutinas en un ámbito determinado. La función `launch()` se utiliza para crear una corrutina que no devuelve un valor, mientras que la función `async()` se utiliza para crear una corrutina que devuelve un valor. Ambas funciones se pueden utilizar en un ámbito de `CoroutineScope` para gestionar la ejecución de corrutinas de manera más eficiente.
:::

## CoroutineContext

En Kotlin, el `CoroutineContext` es una interfaz que define el contexto en el que se ejecuta una corrutina. 

Un `CoroutineContext` se utiliza para definir el despachador, la supervisión y otros elementos del contexto de una corrutina. 

Es en esencia un mapa que almacena pares clave-valor que se utilizan para definir el contexto de una corrutina. 

Estos campos no son obligatorios pero algunos de los más comunes son:
- `Job`: Un objeto que representa una unidad de trabajo que se puede cancelar y supervisar.
- `CoroutineName`: Un objeto que representa el nombre de una corrutina.
- `CoroutineExceptionHandler`: Un objeto que maneja las excepciones que se producen en una corrutina. 
- `Dispatcher`: Un objeto que define en qué hilo o grupo de hilos se ejecuta la corrutina. 

```kotlin
suspend fun main() {
    val context = Dispatchers.Main + Job()
    withContext(context) {
        obtenerDatos()
    }
}
suspend fun obtenerDatos() {
    delay(1000)
    println("Datos obtenidos")
}
```

En el ejemplo anterior, se define una función `obtenerDatos` que simula una operación asíncrona que tarda 1 segundo en completarse. La función `main` utiliza la función `withContext` para ejecutar la función `obtenerDatos` en un contexto que combina el despachador `Dispatchers.Main` y un objeto `Job`.  

## CoroutineDispatcher

En Kotlin, el `CoroutineDispatcher` es una interfaz que define el despachador de una corrutina. 

Un `CoroutineDispatcher` se utiliza para definir en qué hilo o grupo de hilos se ejecuta una corrutina. 

Puede utilizar los despachadores predefinidos de Kotlin, como `Dispatchers.Main`, `Dispatchers.IO` y `Dispatchers.Default`, o crear un despachador personalizado.

Para crear uno personalizado se puede utilizar la clase `ExecutorCoroutineDispatcher` de Kotlin. 

```kotlin

suspend fun main() {
    val dispatcher = Dispatchers.IO
    withContext(dispatcher) {
        obtenerDatos()
    }
}
suspend fun obtenerDatos() {
    delay(1000)
    println("Datos obtenidos")
}
```

En el ejemplo anterior, se define una función `obtenerDatos` que simula una operación asíncrona que tarda 1 segundo en completarse. La función `main` utiliza la función `withContext` para ejecutar la función `obtenerDatos` en un contexto que utiliza el despachador `Dispatchers.IO`.  

