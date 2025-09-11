import CodeBlock from '@theme/CodeBlock';

# Tipos de variables y datos en Kotlin

En Kotlin las variables pueden declararse de dos formas, de forma explícita o de forma implícita. En el caso de las variables explícitas, se debe indicar el tipo de dato que almacenará la variable, mientras que en las variables implícitas, el tipo de dato se infiere automáticamente por el compilador.

:::warning[Sobre la inferencia de tipos]
Que el tipo de dato se infiera automáticamente no quiere decir que Kotlin sea un lenguaje de tipado dinámico, ya que una vez que se asigna un tipo de dato a una variable, no se puede cambiar.
:::

## Variables explícitas

Para declarar una variable de forma explícita en Kotlin, se debe indicar el tipo de dato que almacenará la variable seguido del nombre de la variable y opcionalmente de su valor inicial.

```kotlin   
val nombre: String = "Ejemplo"
val edad: Int = 25
```

En el ejemplo anterior, se declaran dos variables de forma explícita, una de tipo `String` llamada `nombre` y otra de tipo `Int` llamada `edad`.

## Variables implícitas

Para declarar una variable de forma implícita en Kotlin, se debe utilizar la palabra clave `val` o `var` seguida del nombre de la variable y opcionalmente de su valor inicial. En este caso, el tipo de dato se infiere automáticamente por el compilador.

```kotlin
val nombre = "Ejemplo"
val edad = 25
```

En el ejemplo anterior, se declaran dos variables de forma implícita, una de tipo `String` llamada `nombre` y otra de tipo `Int` llamada `edad`.

## Tipos de datos primitivos

En Kotlin, los tipos de datos primitivos son los mismos que en Java, pero con algunas diferencias en la forma en que se declaran.

- **Byte**: Almacena números enteros de 8 bits.
- **Short**: Almacena números enteros de 16 bits.
- **Int**: Almacena números enteros de 32 bits.
- **Long**: Almacena números enteros de 64 bits.
- **Float**: Almacena números de punto flotante de 32 bits.
- **Double**: Almacena números de punto flotante de 64 bits.
- **Char**: Almacena caracteres Unicode de 16 bits.
- **Boolean**: Almacena valores booleanos (`true` o `false`).

```kotlin
val entero: Int = 10
val flotante: Float = 10.5f
val caracter: Char = 'A'
val booleano: Boolean = true
```

## Tipos de datos compuestos

Además de los tipos de datos primitivos, Kotlin también tiene tipos de datos compuestos que permiten almacenar colecciones de datos.

- **Array**: Almacena una colección de elementos del mismo tipo.
- **List**: Almacena una colección de elementos ordenados.
- **Set**: Almacena una colección de elementos únicos.
- **Map**: Almacena una colección de pares clave-valor.

```kotlin
val numeros = arrayOf(1, 2, 3, 4, 5)
val nombres = listOf("Juan", "María", "Pedro")
val colores = setOf("Rojo", "Verde", "Azul")
val edades = mapOf("Juan" to 25, "María" to 30, "Pedro" to 35)
```

## Conversión de tipos

En Kotlin, la conversión de tipos se realiza de forma segura y explícita utilizando funciones específicas para cada tipo de dato.

```kotlin
val numero: Int = 10
val texto: String = numero.toString()

val texto: String = "10"
val numero: Int = texto.toInt()
```

En el primer ejemplo, se convierte un número entero a una cadena de texto utilizando la función `toString()`. En el segundo ejemplo, se convierte una cadena de texto a un número entero utilizando la función `toInt()`.   

## Sobre las variables mutables e inmutables

En Kotlin, las variables se pueden declarar como `val` (inmutables) o `var` (mutables). Las variables inmutables no pueden cambiar su valor una vez asignado, mientras que las variables mutables pueden cambiar su valor en cualquier momento.

```kotlin
val nombre: String = "Ejemplo" // Variable inmutable
var edad: Int = 25 // Variable mutable
```

En el ejemplo anterior, la variable `nombre` es inmutable, por lo que su valor no puede cambiar una vez asignado. La variable `edad`, en cambio, es mutable, por lo que su valor puede cambiar en cualquier momento.    

### Sobre la mutabilidad y la inmutabilidad

La inmutabilidad es una característica importante en Kotlin, ya que ayuda a prevenir los errores de programación al evitar que los valores de las variables cambien de forma inesperada. Al utilizar variables inmutables, se puede escribir código más seguro y predecible, lo que facilita la depuración y el mantenimiento del código.   

Cuándo trabajamos con listas o arrays, podemos modificar los elementos de la lista, pero no podemos cambiar la referencia de la lista. 

```kotlin
val lista = mutableListOf(1, 2, 3, 4, 5)
lista[0] = 10 // Modifica el elemento en la posición 0
lista = mutableListOf(6, 7, 8, 9, 10) // Error de compilación
```

En el ejemplo anterior, se modifica el elemento en la posición `0` de la lista `lista`, pero no se puede cambiar la referencia de la lista. Si se intenta asignar una nueva lista a la variable `lista`, se produce un error de compilación.

Podemos ver también un ejemplo de lista mutable pero cuyo contenido no puede ser modificado.

```kotlin
val lista = listOf(1, 2, 3, 4, 5)
lista[0] = 10 // Error de compilación
```

En el ejemplo anterior, se intenta modificar el elemento en la posición `0` de la lista `lista`, pero como la lista es inmutable, se produce un error de compilación.

Esto nos deja claro que la inmutabilidad no solo se refiere a la variable en sí, sino también a los elementos que contiene la variable.

Podemos entonces tener cuatro situaciones posibles:

- Variable inmutable y elementos inmutables: No se puede cambiar ni la variable ni los elementos.
- Variable inmutable y elementos mutables: No se puede cambiar la variable, pero sí los elementos.
- Variable mutable y elementos inmutables: Se puede cambiar la variable, pero no los elementos.
- Variable mutable y elementos mutables: Se puede cambiar tanto la variable como los elementos.

:::tip[Las variables inmutables y la programación funcional]
Las variables inmutables son una característica fundamental de la programación funcional, ya que permiten escribir código más seguro y predecible al evitar los efectos secundarios y las mutaciones de estado. 

Al utilizar variables inmutables, se puede escribir código más conciso, legible y mantenible, lo que facilita la depuración y el mantenimiento del código.
:::

## Sobre las variables nulas

En Kotlin, las variables pueden ser nulas si se declara con el operador `?`. Esto permite que una variable pueda contener un valor nulo en lugar de un valor no nulo.

```kotlin
val nombre: String? = null
```

En el ejemplo anterior, la variable `nombre` se declara como nula utilizando el operador `?`. Esto significa que la variable `nombre` puede contener un valor nulo en lugar de un valor no nulo.

:::info[Sobre la seguridad de nulos en Kotlin]
El manejo de nulos en Kotlin es una de las características más importantes del lenguaje, ya que ayuda a prevenir los errores de referencia nula que son comunes en otros lenguajes de programación.

Más adelante veremos cómo manejar los valores nulos de forma segura en Kotlin.
:::

## Sobre las variables declaradas como const

En Kotlin, las variables se pueden declarar como `const` para indicar que su valor es constante en tiempo de compilación. Las variables `const` deben ser de tipo `val` y deben estar en el ámbito de un objeto o de un compañero de clase.

```kotlin
const val PI = 3.14159
```

En el ejemplo anterior, se declara una constante `PI` con un valor de `3.14159`. Esta constante es accesible en tiempo de compilación y su valor no puede cambiar en tiempo de ejecución.   

## Sobre las Strings en Kotlin

En Kotlin, las cadenas de texto se pueden declarar utilizando comillas simples (`'`) o comillas dobles (`"`). Las cadenas de texto declaradas con comillas simples son de tipo `Char`, mientras que las declaradas con comillas dobles son de tipo `String`.

```kotlin
val caracter: Char = 'A'
val texto: String = "Ejemplo"
```

En el ejemplo anterior, se declara una variable `caracter` de tipo `Char` con el valor `'A'` y una variable `texto` de tipo `String` con el valor `"Ejemplo"`.

Las Strings en Kotlin se pueden comparar utilizando el operador `==` para comparar el contenido de las cadenas y el operador `===` para comparar las referencias de las cadenas.

```kotlin   
val texto1 = "Hola"
val texto2 = "Hola"

println(texto1 == texto2) // true
println(texto1 === texto2) // true
```

En el ejemplo anterior, se comparan dos cadenas de texto `texto1` y `texto2` utilizando los operadores `==` y `===`. Ambas comparaciones devuelven `true` ya que las cadenas son iguales en contenido y referencia. 

A diferencia de Java, en Kotlin las cadenas de texto son inmutables, lo que significa que una vez que se crea una cadena de texto, no se puede modificar su contenido. Para modificar una cadena de texto en Kotlin, se debe crear una nueva cadena con el contenido modificado.

```kotlin
val texto = "Hola"
val nuevoTexto = texto + " Mundo"
```

En el ejemplo anterior, se crea una nueva cadena de texto `nuevoTexto` concatenando la cadena `texto` con la cadena `" Mundo"`. La cadena `texto` no se modifica, sino que se crea una nueva cadena con el contenido modificado.    

Sin embargo, si comparamos una string con un caracter, Kotlin no permite la comparación directa, ya que son tipos de datos diferentes.

```kotlin
val texto = "H"
val caracter = 'H'

println(texto == caracter) // Error de compilación
```

En el ejemplo anterior, se intenta comparar una cadena de texto `texto` con un caracter `caracter`, lo cual produce un error de compilación ya que los tipos de datos son diferentes.   

