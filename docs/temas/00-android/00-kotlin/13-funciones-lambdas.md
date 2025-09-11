import CodeBlock from '@theme/CodeBlock';

# Funciones y lambdas en Kotlin

En Kotlin, las funciones son ciudadanos de primera clase, lo que significa que puedes tratarlas como cualquier otro tipo de dato, como un `Int` o un `String`. Esto te permite pasar funciones como argumentos a otras funciones, devolver funciones de otras funciones y almacenar funciones en variables. 

Las funciones en Kotlin se definen utilizando la palabra clave `fun`, seguida del nombre de la función, los parámetros de entrada y el tipo de retorno. 

```kotlin
fun suma(a: Int, b: Int): Int {
    return a + b
}
```

En el ejemplo anterior, se define una función `suma` que toma dos parámetros de tipo `Int` y devuelve un valor de tipo `Int`. La función suma los dos parámetros de entrada y devuelve el resultado.

## Funciones de orden superior

En Kotlin, puedes pasar funciones como argumentos a otras funciones. Estas funciones se conocen como funciones de orden superior y te permiten escribir código más conciso y reutilizable. 

```kotlin
fun operacion(a: Int, b: Int, funcion: (Int, Int) -> Int): Int {
    return funcion(a, b)
}

fun suma(a: Int, b: Int): Int {
    return a + b
}

fun resta(a: Int, b: Int): Int {
    return a - b
}

val resultadoSuma = operacion(10, 5, ::suma)
val resultadoResta = operacion(10, 5, ::resta)
```

En el ejemplo anterior, se define una función `operacion` que toma dos parámetros de tipo `Int` y una función de orden superior que toma dos parámetros de tipo `Int` y devuelve un valor de tipo `Int`. La función `operacion` aplica la función de orden superior a los dos parámetros de entrada y devuelve el resultado.

## Funciones lambda

En Kotlin, puedes definir funciones anónimas conocidas como funciones lambda. Las funciones lambda son funciones sin nombre que puedes pasar como argumentos a otras funciones. 

```kotlin
val suma = { a: Int, b: Int -> a + b }
val resta = { a: Int, b: Int -> a - b }

val resultadoSuma = suma(10, 5)
val resultadoResta = resta(10, 5)
```

En el ejemplo anterior, se definen dos funciones lambda `suma` y `resta` que toman dos parámetros de tipo `Int` y devuelven un valor de tipo `Int`. Las funciones lambda se asignan a variables y se pueden utilizar como cualquier otra función.

## Los parámetros en Kotlin

A diferencia de Java, en Kotlin los parámetros de una función son inmutables por defecto, lo que significa que no se pueden modificar dentro de la función. Si necesitas modificar un parámetro dentro de una función, debes declararlo como una variable `var`.

```kotlin
fun duplicar(numero: Int): Int {
    var resultado = numero
    resultado *= 2
    return resultado
}
```

En el ejemplo anterior, se define una función `duplicar` que toma un parámetro de tipo `Int` y devuelve un valor de tipo `Int`. El parámetro `numero` se declara como una variable `var` para poder modificar su valor dentro de la función.

### Parámetros con valores por defecto

Los parámetros de una función en Kotlin pueden tener valores por defecto, lo que te permite llamar a la función sin proporcionar todos los argumentos.

```kotlin
fun saludar(nombre: String = "Mundo") {
    println("Hola, $nombre!")
}

saludar() // Hola, Mundo!
saludar("Juan") // Hola, Juan!
```

En el ejemplo anterior, se define una función `saludar` que toma un parámetro de tipo `String` con un valor por defecto de `"Mundo"`. Si no se proporciona un argumento al llamar a la función, se utiliza el valor por defecto.

:::tip[Funciones con valores por defecto y su no obligatoriedad, su uso en Compose]
Las funciones con valores por defecto son muy útiles en Jetpack Compose, ya que te permiten definir componentes con valores por defecto y llamar a esos componentes sin proporcionar todos los argumentos.

Por ejemplo, puedes definir un botón con un texto por defecto y un color por defecto, y luego llamar a ese botón sin proporcionar el texto o el color si deseas utilizar los valores por defecto.

```kotlin
@Composable
fun Boton(texto: String = "Aceptar", color: Color = Color.Blue) {
    Button(onClick = { /* Acción al hacer clic */ }) {
        Text(texto, color = color)
    }
}

Boton() // Botón con texto "Aceptar" y color azul
Boton("Cancelar", Color.Red) // Botón con texto "Cancelar" y color rojo
```
:::

### Parámetros de una función lambda

En una función lambda en Kotlin, puedes especificar los tipos de los parámetros de entrada o dejar que el compilador infiera los tipos automáticamente.

```kotlin
val suma: (Int, Int) -> Int = { a, b -> a + b }
val resta = { a: Int, b: Int -> a - b }
```

En el ejemplo anterior, se definen dos funciones lambda `suma` y `resta` que toman dos parámetros de tipo `Int` y devuelven un valor de tipo `Int`. En la función lambda `suma`, se especifican los tipos de los parámetros de entrada, mientras que en la función lambda `resta`, se deja que el compilador infiera los tipos automáticamente.

### La palabra reservada `it`

En una función lambda en Kotlin, puedes utilizar la palabra reservada `it` para referirte al único parámetro de entrada si la función lambda tiene un solo parámetro.

```kotlin
val cuadrado: (Int) -> Int = { it * it }
```

En el ejemplo anterior, se define una función lambda `cuadrado` que toma un parámetro de tipo `Int` y devuelve un valor de tipo `Int`. 

:::info[Short explicativo en YouTube]
[Enlace al vídeo](https://youtube.com/shorts/h1SwVhEHAUs?si=Mqk7qXcF8qNsZMWf)
:::

La palabra reservada `it` se utiliza para referirse al único parámetro de entrada de la función lambda.

Esto es útil cuando la función lambda tiene un solo parámetro y quieres hacer el código más conciso.

### Funciones lambda con múltiples líneas

En una función lambda en Kotlin, puedes utilizar múltiples líneas de código si es necesario. 

```kotlin
val suma: (Int, Int) -> Int = { a, b ->
    val resultado = a + b
    println("La suma de $a y $b es $resultado")
    resultado
}
```

En el ejemplo anterior, se define una función lambda `suma` que toma dos parámetros de tipo `Int` y devuelve un valor de tipo `Int`. La función lambda realiza la suma de los dos parámetros y muestra un mensaje por consola con el resultado.

### Número variable de argumentos

En Kotlin, puedes definir funciones que toman un número variable de argumentos utilizando el operador `vararg`.

```kotlin
fun sumar(vararg numeros: Int): Int {
    var suma = 0
    for (numero in numeros) {
        suma += numero
    }
    return suma
}

val resultado = sumar(1, 2, 3, 4, 5)
```

En el ejemplo anterior, se define una función `sumar` que toma un número variable de argumentos de tipo `Int` utilizando el operador `vararg`. La función suma todos los números pasados como argumentos y devuelve el resultado.

## Funciones de extensión (Extension Functions)

En Kotlin, puedes agregar nuevas funciones a las clases existentes sin heredar de ellas. 

Estas funciones se conocen como funciones de extensión y te permiten extender la funcionalidad de las clases sin modificar su código fuente.

```kotlin
fun String.invertir(): String {
    return this.reversed()
}

val texto = "Hola, mundo!"
val textoInvertido = texto.invertir()
```

En el ejemplo anterior, se define una función de extensión `invertir` para la clase `String` que invierte el contenido de la cadena de texto. La función de extensión se llama como si fuera un método de la clase `String`.

:::info[Funciones de extensión y funciones de orden superior]
Las funciones de extensión y las funciones de orden superior son dos características poderosas de Kotlin que te permiten escribir código más conciso y reutilizable.

Las funciones de extensión te permiten agregar nuevas funciones a las clases existentes sin heredar de ellas, mientras que las funciones de orden superior te permiten pasar funciones como argumentos a otras funciones.

Al combinar estas dos características, puedes escribir código más flexible y expresivo en Kotlin.
:::
