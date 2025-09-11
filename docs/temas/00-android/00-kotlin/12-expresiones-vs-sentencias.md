# Expresiones vs sentencias

En Kotlin, las expresiones y las sentencias son dos conceptos fundamentales que se utilizan para definir el flujo de control y la lógica de un programa. Aunque ambos se utilizan para realizar operaciones y tomar decisiones en un programa, existen diferencias importantes entre ellos que es importante comprender.    

## Expresiones

En Kotlin, una expresión es una combinación de valores, variables, operadores y funciones que se evalúa para producir un resultado. Las expresiones pueden ser tan simples como una constante o tan complejas como una llamada a una función que devuelve un valor. 

Las expresiones en Kotlin pueden tener un valor de retorno, lo que significa que pueden ser utilizadas en cualquier lugar donde se espere un valor, como en la inicialización de una variable, en una sentencia de control de flujo o en una llamada a una función. 

```kotlin
val numero = 10
val resultado = numero * 2
```

En el ejemplo anterior, la expresión `numero * 2` se evalúa para producir un resultado que se asigna a la variable `resultado`. La expresión `numero * 2` es una expresión aritmética que multiplica el valor de la variable `numero` por `2` y devuelve el resultado.  

## Sentencias

En Kotlin, una sentencia es una instrucción que realiza una acción en un programa. Las sentencias pueden ser tan simples como una asignación de variable o tan complejas como una estructura de control de flujo que toma decisiones basadas en condiciones.    

Las sentencias en Kotlin no tienen un valor de retorno, lo que significa que no pueden ser utilizadas en lugares donde se espere un valor, como en la inicialización de una variable o en una llamada a una función. 

```kotlin
val numero = 10
if (numero > 0) {
    println("El número es positivo")
} else {
    println("El número es negativo")
}
```

En el ejemplo anterior, la sentencia `if (numero > 0) { ... } else { ... }` es una estructura de control de flujo que toma una decisión basada en la condición `numero > 0`. La sentencia `if` evalúa la condición y ejecuta el bloque de código correspondiente si la condición es verdadera.  

## Diferencias entre expresiones y sentencias

Las principales diferencias entre expresiones y sentencias en Kotlin son las siguientes:

- **Valor de retorno**: Las expresiones tienen un valor de retorno y pueden ser utilizadas en lugares donde se espere un valor, mientras que las sentencias no tienen un valor de retorno y no pueden ser utilizadas en lugares donde se espere un valor.

- **Complejidad**: Las expresiones pueden ser tan simples como una constante o tan complejas como una llamada a una función que devuelve un valor, mientras que las sentencias realizan acciones en un programa y pueden ser tan simples como una asignación de variable o tan complejas como una estructura de control de flujo.

- **Uso**: Las expresiones se utilizan para realizar operaciones y producir resultados, mientras que las sentencias se utilizan para realizar acciones en un programa, como asignar valores a variables, tomar decisiones basadas en condiciones o repetir bloques de código.

:::tip[La ventaja de las expresiones en la programación funcional]
En la programación funcional, se fomenta el uso de expresiones en lugar de sentencias, ya que las expresiones son más concisas, legibles y fáciles de entender. 

Al utilizar expresiones en lugar de sentencias, se puede escribir menos código y hacer más cosas.

Esto es especialmente útil en Kotlin, que es un lenguaje de programación funcional que fomenta el uso de expresiones para realizar operaciones y producir resultados de forma concisa y eficiente.
:::

### Ejemplo de una función que usa sentencias (statement) y expresiones (expression)

Pongamos el ejemplo de una función que dictamina si una persona es mayor de edad o no. 

```kotlin title="Versión con sentencias"
fun esMayorDeEdad(edad: Int): Boolean {
    if (edad >= 18) {
        return true
    } else {
        return false
    }
}
```

En este caso, la función `esMayorDeEdad` utiliza sentencias para tomar una decisión basada en la edad de la persona y devolver un valor booleano que indica si la persona es mayor de edad o no.

```kotlin title="Versión con expresiones"
fun esMayorDeEdad(edad: Int): Boolean = edad >= 18
```

En este caso, la función `esMayorDeEdad` utiliza una expresión para evaluar si la edad de la persona es mayor o igual a `18` y devolver un valor booleano que indica si la persona es mayor de edad o no.

Cómo se puede observar, la versión con expresiones es más concisa y legible que la versión con sentencias, ya que utiliza una expresión para realizar la misma operación de forma más eficiente y clara.


