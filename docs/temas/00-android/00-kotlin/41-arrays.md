import CodeBlock from '@theme/CodeBlock';

# Arrays en Kotlin

En Kotlin, puedes crear arrays utilizando la función `arrayOf()`. Los arrays en Kotlin son inmutables por defecto, lo que significa que no puedes modificar su tamaño una vez que se han creado.

```kotlin
val numeros = arrayOf(1, 2, 3, 4, 5)
```

En el ejemplo anterior, se crea un array `numeros` con los valores `1, 2, 3, 4, 5`. Los arrays en Kotlin se crean utilizando la función `arrayOf()` seguida de los valores del array entre paréntesis.

## Acceso a elementos de un array

Puedes acceder a elementos individuales de un array utilizando el operador de indexación `[]` seguido del índice del elemento que deseas acceder.

```kotlin
val primerNumero = numeros[0]
val segundoNumero = numeros[1]
```

En el ejemplo anterior, se accede al primer y segundo elemento del array `numeros` utilizando los índices `0` y `1` respectivamente.

## Modificación de elementos de un array

Para modificar un elemento de un array, puedes utilizar el operador de indexación `[]` seguido del índice del elemento que deseas modificar.

```kotlin
numeros[0] = 10
```

En el ejemplo anterior, se modifica el primer elemento del array `numeros` asignándole el valor `10`.

## Iteración sobre un array

Puedes iterar sobre los elementos de un array utilizando un bucle `for` o la función `forEach()`.

```kotlin
for (numero in numeros) {
    println(numero)
}

numeros.forEach { numero ->
    println(numero)
}
```

En el ejemplo anterior, se itera sobre los elementos del array `numeros` utilizando un bucle `for` y la función `forEach()`.

## Arrays de tipos primitivos

En Kotlin, puedes utilizar arrays de tipos primitivos como `IntArray`, `DoubleArray`, `BooleanArray`, etc. para mejorar el rendimiento de tu código.

```kotlin
val enteros = intArrayOf(1, 2, 3, 4, 5)
val dobles = doubleArrayOf(1.0, 2.0, 3.0, 4.0, 5.0)
val booleanos = booleanArrayOf(true, false, true, false)
```

En el ejemplo anterior, se crean arrays de tipos primitivos `IntArray`, `DoubleArray` y `BooleanArray` con valores iniciales.

## Operaciones con arrays

Los arrays en Kotlin proporcionan una serie de operaciones útiles, como `size` para obtener el tamaño del array, `contains` para comprobar si un elemento está presente en el array, `indexOf` para obtener el índice de un elemento en el array, etc.

```kotlin
val tamaño = numeros.size
val contieneTres = numeros.contains(3)
val indiceDeCuatro = numeros.indexOf(4)
```

En el ejemplo anterior, se obtiene el tamaño del array `numeros`, se comprueba si el array contiene el número `3` y se obtiene el índice del número `4` en el array.
