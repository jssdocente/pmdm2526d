import CodeBlock from '@theme/CodeBlock';

# Maps en Kotlin

En Kotlin, un `Map` es una colección de pares clave-valor donde cada clave está asociada a un valor. Los `Map` en Kotlin son inmutables por defecto, lo que significa que no puedes modificar su tamaño una vez que se han creado.

```kotlin
val numeros = mapOf("uno" to 1, "dos" to 2, "tres" to 3)
```

En el ejemplo anterior, se crea un `Map` `numeros` con los pares clave-valor `"uno" -> 1`, `"dos" -> 2` y `"tres" -> 3`. Los `Map` en Kotlin se crean utilizando la función `mapOf()` seguida de los pares clave-valor entre paréntesis.

## Acceso a elementos de un Map

Puedes acceder a elementos individuales de un `Map` utilizando la clave del elemento que deseas acceder.

```kotlin
val numeroUno = numeros["uno"]
val numeroDos = numeros["dos"]
```

En el ejemplo anterior, se accede a los valores asociados a las claves `"uno"` y `"dos"` del `Map` `numeros`.

## Iteración sobre un Map

Puedes iterar sobre los pares clave-valor de un `Map` utilizando un bucle `for` o la función `forEach()`.

```kotlin
for ((clave, valor) in numeros) {
    println("Clave: $clave, Valor: $valor")
}

numeros.forEach { (clave, valor) ->
    println("Clave: $clave, Valor: $valor")
}
```

En el ejemplo anterior, se itera sobre los pares clave-valor del `Map` `numeros` utilizando un bucle `for` y la función `forEach()`.

## Maps de tipos primitivos

En Kotlin, puedes utilizar `Map` de tipos primitivos como `IntMap`, `DoubleMap`, `BooleanMap`, etc. para mejorar el rendimiento de tu código.

```kotlin
val enteros = intMapOf(1 to "uno", 2 to "dos", 3 to "tres")
val dobles = doubleMapOf(1.0 to "uno", 2.0 to "dos", 3.0 to "tres")
val booleanos = booleanMapOf(true to "verdadero", false to "falso")
```

En el ejemplo anterior, se crean `Map` de tipos primitivos `IntMap`, `DoubleMap` y `BooleanMap` con pares clave-valor iniciales.

## Modificación de elementos de un Map

Los `Map` en Kotlin son inmutables por defecto, por lo que no puedes modificar los elementos de un `Map` una vez que se ha creado. Si necesitas un `Map` mutable, puedes utilizar la función `mutableMapOf()`.

```kotlin
val numeros = mutableMapOf("uno" to 1, "dos" to 2, "tres" to 3)
numeros["uno"] = 10
```

En el ejemplo anterior, se crea un `Map` mutable `numeros` con los pares clave-valor `"uno" -> 1`, `"dos" -> 2` y `"tres" -> 3`. Se modifica el valor asociado a la clave `"uno"` asignándole el valor `10`.

## Operaciones con Maps

Los `Map` en Kotlin proporcionan una serie de operaciones útiles, como `get()`, `containsKey()`, `containsValue()`, `keys`, `values`, `filterKeys()`, `filterValues()`, etc.

```kotlin
val valorUno = numeros.get("uno")
val contieneDos = numeros.containsKey("dos")
val contieneCinco = numeros.containsValue(5)
val claves = numeros.keys
val valores = numeros.values
val numerosPares = numeros.filterKeys { clave -> clave.length % 2 == 0 }
val numerosImpares = numeros.filterValues { valor -> valor % 2 != 0 }
```

En el ejemplo anterior, se utilizan las operaciones `get()`, `containsKey()`, `containsValue()`, `keys`, `values`, `filterKeys()` y `filterValues()` para realizar operaciones comunes con el `Map` `numeros`.

Estas operaciones sirven para lo siguiente:

- `get()`: Obtiene el valor asociado a una clave.
- `containsKey()`: Comprueba si una clave está presente en el `Map`.
- `containsValue()`: Comprueba si un valor está presente en el `Map`.
- `keys`: Obtiene las claves del `Map`.
- `values`: Obtiene los valores del `Map`.
- `filterKeys()`: Filtra los pares clave-valor del `Map` por las claves.
- `filterValues()`: Filtra los pares clave-valor del `Map` por los valores.

Los `Map` en Kotlin son una forma eficiente de almacenar y acceder a datos asociados a claves. Puedes utilizar `Map` para representar relaciones entre objetos y realizar operaciones comunes con ellos de forma sencilla y eficiente.

Para más información sobre los `Map` en Kotlin, puedes consultar la [documentación oficial de Kotlin sobre Maps](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/).`;
