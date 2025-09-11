import CodeBlock from '@theme/CodeBlock';

# Sets en Kotlin

En Kotlin, un `Set` es una colección de elementos únicos, lo que significa que no puede contener elementos duplicados. Los `Set` en Kotlin son inmutables por defecto, lo que significa que no puedes modificar su tamaño una vez que se han creado.

```kotlin
val numeros = setOf(1, 2, 3, 4, 5)
```

En el ejemplo anterior, se crea un `Set` `numeros` con los valores `1, 2, 3, 4, 5`. Los `Set` en Kotlin se crean utilizando la función `setOf()` seguida de los valores del `Set` entre paréntesis.

## Acceso a elementos de un Set

Puedes acceder a elementos individuales de un `Set` utilizando la función `contains()` para comprobar si un elemento está presente en el `Set`.

```kotlin
val contieneTres = numeros.contains(3)
val contieneSeis = numeros.contains(6)
```

En el ejemplo anterior, se comprueba si el `Set` `numeros` contiene los elementos `3` y `6` utilizando la función `contains()`.

## Iteración sobre un Set

Puedes iterar sobre los elementos de un `Set` utilizando un bucle `for` o la función `forEach()`.

```kotlin
for (numero in numeros) {
    println(numero)
}

numeros.forEach { numero ->
    println(numero)
}
```

En el ejemplo anterior, se itera sobre los elementos del `Set` `numeros` utilizando un bucle `for` y la función `forEach()`.

## Sets de tipos primitivos

En Kotlin, puedes utilizar `Set` de tipos primitivos como `IntSet`, `DoubleSet`, `BooleanSet`, etc. para mejorar el rendimiento de tu código.

```kotlin
val enteros = intSetOf(1, 2, 3, 4, 5)
val dobles = doubleSetOf(1.0, 2.0, 3.0, 4.0, 5.0)
val booleanos = booleanSetOf(true, false, true, false)
```

En el ejemplo anterior, se crean `Set` de tipos primitivos `IntSet`, `DoubleSet` y `BooleanSet` con valores iniciales.

## Modificación de elementos de un Set

Los `Set` en Kotlin son inmutables por defecto, por lo que no puedes modificar los elementos de un `Set` una vez que se ha creado. Si necesitas un `Set` mutable, puedes utilizar la función `mutableSetOf()`.

```kotlin
val numeros = mutableSetOf(1, 2, 3, 4, 5)
numeros.add(6)
```

En el ejemplo anterior, se crea un `Set` mutable `numeros` con los valores `1, 2, 3, 4, 5` y se añade el valor `6` al `Set` utilizando la función `add()`.

## Operaciones con Sets

Los `Set` en Kotlin proporcionan una serie de operaciones útiles, como `size` para obtener el tamaño del `Set`, `contains` para comprobar si un elemento está presente en el `Set`, `union` para unir dos `Set`, `intersect` para obtener la intersección de dos `Set`, etc.

```kotlin
val numerosPares = setOf(2, 4, 6, 8, 10)
val numerosImpares = setOf(1, 3, 5, 7, 9)

val union = numeros.union(numerosPares)
val interseccion = numeros.intersect(numerosImpares)
val diferencia = numeros.subtract(numerosPares)
val diferenciaSimetrica = numeros.symmetricDifference(numerosPares)
val contieneTodos = numeros.containsAll(numerosPares)
val contieneAlguno = numeros.containsAny(numerosPares)
val esSubconjunto = numeros.isSubset(numerosPares)
val esSuperconjunto = numeros.isSuperset(numerosPares)
val esDisjunto = numeros.isDisjoint(numerosPares)
val esVacio = numeros.isEmpty()
```

En el ejemplo anterior, se utilizan las operaciones `union()` e `intersect()` para realizar operaciones comunes con los `Set` `numeros`, `numerosPares` y `numerosImpares`.

Estas operaciones sirven para lo siguiente:

- `union()`: Devuelve un `Set` que contiene todos los elementos de los dos `Set`.
- `intersect()`: Devuelve un `Set` que contiene los elementos comunes de los dos `Set`.
- `subtract()`: Devuelve un `Set` que contiene los elementos del primer `Set` que no están en el segundo `Set`.
- `symmetricDifference()`: Devuelve un `Set` que contiene los elementos que están en uno de los `Set` pero no en ambos.
- `containsAll()`: Comprueba si un `Set` contiene todos los elementos de otro `Set`.
- `containsAny()`: Comprueba si un `Set` contiene al menos un elemento de otro `Set`.
- `isSubset()`: Comprueba si un `Set` es un subconjunto de otro `Set`.
- `isSuperset()`: Comprueba si un `Set` es un superconjunto de otro `Set`.
- `isDisjoint()`: Comprueba si dos `Set` son disjuntos, es decir, si no tienen elementos en común.
- `isEmpty()`: Comprueba si un `Set` está vacío.

Los `Set` en Kotlin son una forma eficiente de almacenar y manipular colecciones de elementos únicos. Puedes utilizar las operaciones proporcionadas por los `Set` para realizar operaciones comunes, como unir, intersecar, restar y comparar `Set` entre sí.

Si necesitas un `Set` mutable, puedes utilizar la función `mutableSetOf()` para crear un `Set` que puedas modificar. Los `Set` en Kotlin son una herramienta poderosa que te permite trabajar con colecciones de elementos únicos de forma eficiente y concisa.


