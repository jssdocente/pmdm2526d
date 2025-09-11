import CodeBlock from '@theme/CodeBlock';

# Listas en Kotlin

En Kotlin, puedes crear listas utilizando la función `listOf()`. Las listas en Kotlin son inmutables por defecto, lo que significa que no puedes modificar su tamaño una vez que se han creado.

```kotlin
val numeros = listOf(1, 2, 3, 4, 5)
```

En el ejemplo anterior, se crea una lista `numeros` con los valores `1, 2, 3, 4, 5`. Las listas en Kotlin se crean utilizando la función `listOf()` seguida de los valores de la lista entre paréntesis.

## Acceso a elementos de una lista

Puedes acceder a elementos individuales de una lista utilizando el operador de indexación `[]` seguido del índice del elemento que deseas acceder.

```kotlin
val primerNumero = numeros[0]
val segundoNumero = numeros[1]
```

En el ejemplo anterior, se accede al primer y segundo elemento de la lista `numeros` utilizando los índices `0` y `1` respectivamente.

## Iteración sobre una lista

Puedes iterar sobre los elementos de una lista utilizando un bucle `for` o la función `forEach()`.

```kotlin
for (numero in numeros) {
    println(numero)
}

numeros.forEach { numero ->
    println(numero)
}
```

En el ejemplo anterior, se itera sobre los elementos de la lista `numeros` utilizando un bucle `for` y la función `forEach()`.

## Listas de tipos primitivos

En Kotlin, puedes utilizar listas de tipos primitivos como `IntList`, `DoubleList`, `BooleanList`, etc. para mejorar el rendimiento de tu código.

```kotlin
val enteros = intListOf(1, 2, 3, 4, 5)
val dobles = doubleListOf(1.0, 2.0, 3.0, 4.0, 5.0)
val booleanos = booleanListOf(true, false, true, false)
```

En el ejemplo anterior, se crean listas de tipos primitivos `IntList`, `DoubleList` y `BooleanList` con valores iniciales.

## Modificación de elementos de una lista

Las listas en Kotlin son inmutables por defecto, por lo que no puedes modificar los elementos de una lista una vez que se ha creado. Si necesitas una lista mutable, puedes utilizar la función `mutableListOf()`.

```kotlin
val numeros = mutableListOf(1, 2, 3, 4, 5)
numeros[0] = 10
```

En el ejemplo anterior, se crea una lista mutable `numeros` con los valores `1, 2, 3, 4, 5`. Se modifica el primer elemento de la lista asignándole el valor `10`.

## Operaciones comunes con listas

Kotlin proporciona una serie de funciones de extensión para realizar operaciones comunes con listas, como `map()`, `filter()`, `reduce()`, `sortedBy()`, etc.

```kotlin
val cuadrados = numeros.map { numero -> numero * numero }
val pares = numeros.filter { numero -> numero % 2 == 0 }
val suma = numeros.reduce { acc, numero -> acc + numero }
val ordenados = numeros.sortedBy { numero -> numero }
```

En el ejemplo anterior, se utilizan las funciones de extensión `map()`, `filter()`, `reduce()` y `sortedBy()` para realizar operaciones comunes con la lista `numeros`.

Estás funciones hacen lo siguiente:

- `map()`: Aplica una función a cada elemento de la lista y devuelve una nueva lista con los resultados.

- `filter()`: Filtra los elementos de la lista que cumplen una condición dada y devuelve una nueva lista con los elementos filtrados.

- `reduce()`: Combina los elementos de la lista en un solo valor utilizando una función dada.

- `sortedBy()`: Ordena los elementos de la lista en función de una clave dada y devuelve una nueva lista ordenada.

- `groupBy()`: Agrupa los elementos de la lista en función de una clave dada y devuelve un mapa de claves y listas de elementos.

Otros ejemplos de operaciones comunes con listas son `sum()`, `max()`, `min()`, `average()`, `distinct()`, etc.

```kotlin
val suma = numeros.sum()
val maximo = numeros.max()
val minimo = numeros.min()
val promedio = numeros.average()
val unicos = numeros.distinct()
```

En el ejemplo anterior, se utilizan las funciones de extensión `sum()`, `max()`, `min()`, `average()` y `distinct()` para realizar operaciones comunes con la lista `numeros`.

Su funcionalidad es la siguiente:

- `sum()`: Calcula la suma de los elementos de la lista.

- `max()`: Devuelve el valor máximo de la lista.

- `min()`: Devuelve el valor mínimo de la lista.

- `average()`: Calcula el promedio de los elementos de la lista.

- `distinct()`: Devuelve una nueva lista con los elementos únicos de la lista original.

## Funciones de extensión para listas

En Kotlin, puedes crear tus propias funciones de extensión para realizar operaciones específicas con listas.

```kotlin
fun List<Int>.duplicar(): List<Int> {
    return this.map { it * 2 }
}

val duplicados = numeros.duplicar()
```

En el ejemplo anterior, se define una función de extensión `duplicar()` que duplica cada elemento de una lista de enteros. La función de extensión se llama con la lista `numeros` y devuelve una nueva lista con los elementos duplicados.
