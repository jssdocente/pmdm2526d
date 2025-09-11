import CodeBlock from '@theme/CodeBlock';

# Data classes en Kotlin

En Kotlin, puedes crear clases de datos utilizando la palabra clave `data`. Las clases de datos son una forma concisa de definir clases que contienen datos y no tienen lógica adicional.

```kotlin
data class Persona(val nombre: String, val edad: Int)
```

En el ejemplo anterior, se define una clase de datos `Persona` que tiene dos propiedades `nombre` y `edad`. La clase de datos se crea utilizando la palabra clave `data` seguida del nombre de la clase y las propiedades de la clase.

:::info[Short explicativo en YouTube]
[Enlace al vídeo](https://youtube.com/shorts/W-oBld3JNWk?si=2yRcnnElE5dfeYGy)
:::

:::info[vídeo explicativo en YouTube]
<iframe width="560" height="315" src="https://www.youtube.com/embed/wiQrSGa4tBI?si=vplFQK_jsJwbjh95" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
:::


## Propiedades de las clases de datos

Las clases de datos en Kotlin tienen las siguientes propiedades:

- **Propiedades de solo lectura**: Las propiedades de una clase de datos son de solo lectura y no se pueden modificar una vez que se han inicializado.

- **Métodos `toString()`, `equals()` y `hashCode()`**: Las clases de datos en Kotlin generan automáticamente los métodos `toString()`, `equals()` y `hashCode()` a partir de las propiedades de la clase.

- **Métodos `componentN()`**: Las clases de datos en Kotlin generan automáticamente métodos `componentN()` que te permiten desestructurar objetos de la clase en variables individuales.

- **Método `copy()`**: Las clases de datos en Kotlin generan automáticamente un método `copy()` que te permite crear copias de objetos de la clase con propiedades modificadas.

## Creación de objetos de clases de datos

Para crear un objeto de una clase de datos en Kotlin, puedes utilizar la palabra clave `data` seguida del nombre de la clase y los valores de las propiedades.

```kotlin
val persona = Persona("Juan", 25)
```

En el ejemplo anterior, se crea un objeto de la clase de datos `Persona` con el nombre `Juan` y la edad `25`.

## Desestructuración de objetos de clases de datos

En Kotlin, puedes desestructurar objetos de clases de datos en variables individuales utilizando la notación de desestructuración.

```kotlin
val (nombre, edad) = persona
```

En el ejemplo anterior, se desestructura el objeto `persona` en las variables `nombre` y `edad`.

## Copia de objetos de clases de datos

En Kotlin, puedes crear copias de objetos de clases de datos utilizando el método `copy()`.

```kotlin
val persona2 = persona.copy(edad = 30)
```

En el ejemplo anterior, se crea una copia del objeto `persona` con la edad modificada a `30`.

