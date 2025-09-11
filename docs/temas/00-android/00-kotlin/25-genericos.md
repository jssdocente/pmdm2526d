import CodeBlock from '@theme/CodeBlock';

# Genéricos en Kotlin

En Kotlin, puedes utilizar genéricos para crear clases, funciones e interfaces que trabajen con tipos de datos de forma genérica. Los genéricos te permiten escribir código que es reutilizable y flexible, ya que puedes definir clases y funciones que trabajen con cualquier tipo de datos.

:::info[Video explicativo en YouTube]
<iframe width="560" height="315" src="https://www.youtube.com/embed/XPVa1vTJQts?si=ZmmU6D0RHGs84G5O" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
:::

## Clases genéricas

En Kotlin, puedes crear clases genéricas utilizando la palabra clave `class` seguida de los parámetros de tipo entre corchetes `<>`. Los parámetros de tipo se utilizan para definir los tipos de datos que la clase puede manejar.

```kotlin
class Caja<T>(val contenido: T) {
    fun obtenerContenido(): T {
        return contenido
    }
}

val cajaEntero = Caja(10)
val cajaCadena = Caja("Hola")

val entero: Int = cajaEntero.obtenerContenido()
val cadena: String = cajaCadena.obtenerContenido()
```

En el ejemplo anterior, se define una clase genérica `Caja` que tiene un parámetro de tipo `T`. La clase `Caja` tiene una propiedad `contenido` de tipo `T` y un método `obtenerContenido` que devuelve el contenido de la caja.

Se crean dos instancias de la clase `Caja` con tipos de datos diferentes: una con un entero y otra con una cadena. Se obtiene el contenido de cada caja y se asigna a variables de tipo `Int` y `String`.

## Funciones genéricas

En Kotlin, puedes crear funciones genéricas utilizando la palabra clave `fun` seguida de los parámetros de tipo entre corchetes `<>`. Los parámetros de tipo se utilizan para definir los tipos de datos que la función puede manejar.

```kotlin
fun <T> imprimirElemento(elemento: T) {
    println(elemento)
}

imprimirElemento(10)
imprimirElemento("Hola")
```

En el ejemplo anterior, se define una función genérica `imprimirElemento` que toma un parámetro de tipo `T` y lo imprime en la consola. La función `imprimirElemento` se llama dos veces con un entero y una cadena como argumentos.

## Interfaces genéricas

En Kotlin, puedes crear interfaces genéricas utilizando la palabra clave `interface` seguida de los parámetros de tipo entre corchetes `<>`. Los parámetros de tipo se utilizan para definir los tipos de datos que la interfaz puede manejar.

```kotlin
interface Contenedor<T> {
    fun obtenerContenido(): T
}

class Caja<T>(val contenido: T) : Contenedor<T> {
    override fun obtenerContenido(): T {
        return contenido
    }
}

val cajaEntero = Caja(10)
val entero: Int = cajaEntero.obtenerContenido()
```

En el ejemplo anterior, se define una interfaz genérica `Contenedor` que tiene un parámetro de tipo `T`. La interfaz `Contenedor` define un método `obtenerContenido` que devuelve el contenido del contenedor.

Se crea una clase `Caja` que implementa la interfaz `Contenedor` con un tipo de dato `T`. Se crea una instancia de la clase `Caja` con un entero y se obtiene el contenido de la caja.

## Variance en genéricos

En Kotlin, puedes utilizar la anotación `in` y `out` para especificar la variabilidad de los parámetros de tipo en clases y funciones genéricas.

- `in`: Indica que el parámetro de tipo solo se utiliza en posiciones de entrada (como parámetros de métodos).
- `out`: Indica que el parámetro de tipo solo se utiliza en posiciones de salida (como valores de retorno de métodos).

```kotlin
interface Contenedor<out T> {
    fun obtenerContenido(): T
}

class Caja<in T>(val contenido: T) {
    fun ponerContenido(nuevoContenido: T) {
        // ...
    }
}
```

En el ejemplo anterior, se define una interfaz `Contenedor` con un parámetro de tipo `T` que solo se utiliza en posiciones de salida. La clase `Caja` tiene un parámetro de tipo `T` que solo se utiliza en posiciones de entrada.

## Restricciones en genéricos

En Kotlin, puedes utilizar restricciones para limitar los tipos de datos que se pueden utilizar en clases y funciones genéricas. Puedes utilizar restricciones para garantizar que los tipos de datos cumplan ciertos requisitos.

```kotlin
fun <T : Number> sumar(a: T, b: T): T {
    return a + b
}

val resultadoEntero = sumar(1, 2)
val resultadoFlotante = sumar(1.5, 2.5)
```

En el ejemplo anterior, se define una función `sumar` que toma dos parámetros de tipo `T` que deben ser subtipos de `Number`. La función `sumar` devuelve la suma de los dos parámetros.

Se llama a la función `sumar` con un entero y un flotante como argumentos, y se asigna el resultado a variables de tipo `Int` y `Float`.

## Genéricos en clases y funciones

En Kotlin, los genéricos te permiten escribir código que es reutilizable y flexible, ya que puedes definir clases y funciones que trabajen con cualquier tipo de datos. Puedes utilizar genéricos en clases, funciones e interfaces para crear código genérico y flexible.

```kotlin
class Caja<T>(val contenido: T) {
    fun obtenerContenido(): T {
        return contenido
    }
}

fun <T> imprimirElemento(elemento: T) {
    println(elemento)
}

interface Contenedor<T> {
    fun obtenerContenido(): T
}

fun <T : Number> sumar(a: T, b: T): T {
    return a + b
}
```

En el ejemplo anterior, se muestran ejemplos de clases genéricas, funciones genéricas e interfaces genéricas en Kotlin. Puedes utilizar genéricos para escribir código que sea reutilizable y flexible, ya que puedes definir clases y funciones que trabajen con cualquier tipo de datos.

## Recursos adicionales

- [Documentación oficial de Kotlin sobre genéricos](https://kotlinlang.org/docs/generics.html)
- [Tutorial de Kotlin sobre genéricos](https://play.kotlinlang.org/byExample/01_introduction/06_Generics)
- [Ejemplos de genéricos en Kotlin](https://github.com/resuadam2/kotlin-apuntes/blob/main/src/main/kotlin/p2_oop/Generics.kt)