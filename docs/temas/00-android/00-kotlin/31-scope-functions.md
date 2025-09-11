import CodeBlock from '@theme/CodeBlock';

# Scope functions en Kotlin

En Kotlin, los *scope functions* son funciones que te permiten ejecutar un bloque de código en el contexto de un objeto. Estas funciones te permiten acceder a las propiedades y métodos del objeto de forma más concisa y legible. 

Las *scope functions* en Kotlin son las siguientes: 

- `let`
- `run`
- `with`
- `apply`
- `also`

:::info[Vídeo explicativo en YouTube]
<iframe width="560" height="315" src="https://www.youtube.com/embed/Fz81c3Ywb7Y?si=msfBqTl6DcymqYOx" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
:::


## `let`

La función `let` te permite ejecutar un bloque de código en el contexto de un objeto y devolver el resultado de la última expresión del bloque. 

La función `let` se utiliza para realizar operaciones en un objeto y devolver un resultado.

```kotlin
val resultado = persona?.let {
    println("Nombre: ${it.nombre}")
    println("Edad: ${it.edad}")
    it.edad + 1
}
```

En el ejemplo anterior, se utiliza la función `let` para acceder a las propiedades `nombre` y `edad` del objeto `persona` y devolver la edad incrementada en 1.

## `run`

A diferencia de `let`, la función `run` te permite acceder a las propiedades y métodos del objeto sin necesidad de utilizar `it`.

La función `run` se utiliza para realizar operaciones en un objeto y devolver un resultado.

```kotlin
val resultado = persona?.run {
    println("Nombre: $nombre")
    println("Edad: $edad")
    edad + 1
}
```

En el ejemplo anterior, se utiliza la función `run` para acceder a las propiedades `nombre` y `edad` del objeto `persona` y devolver la edad incrementada en 1.

## `with`

La función `with` es similar a `run`, pero se utiliza con un objeto como argumento en lugar de un receptor.

La función `with` se utiliza para realizar operaciones en un objeto y devolver un resultado.

```kotlin
val resultado = with(persona) {
    println("Nombre: $nombre")
    println("Edad: $edad")
    edad + 1
}
```

En el ejemplo anterior, se utiliza la función `with` para acceder a las propiedades `nombre` y `edad` del objeto `persona` y devolver la edad incrementada en 1.

## `apply`

A diferencia de las funciones anteriores, la función `apply` se utiliza para realizar operaciones en un objeto y devolver el objeto modificado.

La función `apply` se utiliza para realizar operaciones en un objeto y devolver el objeto modificado.

```kotlin
val resultado = persona?.apply {
    println("Nombre: $nombre")
    println("Edad: $edad")
    edad += 1
}
```

En el ejemplo anterior, se utiliza la función `apply` para acceder a las propiedades `nombre` y `edad` del objeto `persona` y modificar la edad incrementándola en 1.

## `also`

La función `also` es similar a `apply`, pero se utiliza con un objeto como argumento en lugar de un receptor.

La función `also` se utiliza para realizar operaciones en un objeto y devolver el objeto original.

```kotlin
val resultado = persona?.also {
    println("Nombre: ${it.nombre}")
    println("Edad: ${it.edad}")
}
```

En el ejemplo anterior, se utiliza la función `also` para acceder a las propiedades `nombre` y `edad` del objeto `persona` y devolver el objeto original.

## Resumen

En resumen, las *scope functions* en Kotlin te permiten ejecutar un bloque de código en el contexto de un objeto y realizar operaciones en el objeto de forma más concisa y legible. Cada función tiene un propósito específico y se utiliza en diferentes situaciones, dependiendo de tus necesidades. 

Podríamos decir que las diferencias generales son las siguientes:

- `let` se utiliza para realizar operaciones en un objeto y devolver un resultado.
- `run` se utiliza para acceder a las propiedades y métodos del objeto sin necesidad de utilizar `it`.
- `with` es similar a `run`, pero se utiliza con un objeto como argumento en lugar de un receptor.
- `apply` se utiliza para realizar operaciones en un objeto y devolver el objeto modificado.
- `also` es similar a `apply`, pero se utiliza con un objeto como argumento en lugar de un receptor.

En la siguiente imagen se muestra un resumen visual de las diferencias entre las *scope functions*:

![Scope functions en Kotlin](https://miro.medium.com/v2/resize:fit:1200/1*OForiGdp4oIicDzbDOgQWg.jpeg)