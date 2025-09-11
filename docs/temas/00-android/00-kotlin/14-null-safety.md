import CodeBlock from '@theme/CodeBlock';

# Null safety en Kotlin

En Kotlin, el manejo de nulos es una parte fundamental del lenguaje. 

Kotlin está diseñado para evitar los errores de referencia nula que son comunes en otros lenguajes de programación, como Java. 

En Kotlin, los tipos de datos pueden ser nulos o no nulos, lo que te permite expresar de forma segura si un valor puede ser nulo o no.  

:::info[Diferencia con Java]
En Java, todos los tipos de datos pueden ser nulos, lo que puede llevar a errores de referencia nula si no se manejan correctamente. 

En Kotlin, los tipos de datos no nulos deben ser manejados de forma explícita, lo que ayuda a prevenir los errores de referencia nula.
:::

## Tipos de datos nulos

En Kotlin, los tipos de datos pueden ser nulos o no nulos.

- **Tipos de datos no nulos**: Los tipos de datos no nulos no pueden contener valores nulos y deben ser inicializados con un valor no nulo.

- **Tipos de datos nulos**: Los tipos de datos nulos pueden contener valores nulos y deben ser inicializados con un valor nulo o con la función `null`.

```kotlin
val nombre: String = "Juan" // Tipo de dato no nulo

val apellido: String? = null // Tipo de dato nulo
```

En el ejemplo anterior, la variable `nombre` es de tipo `String` no nulo, por lo que no puede contener un valor nulo. La variable `apellido`, en cambio, es de tipo `String?` nulo, por lo que puede contener un valor nulo.

## Operadores de seguridad de nulos

En Kotlin, puedes utilizar operadores de seguridad de nulos para manejar los valores nulos de forma segura.

- **Operador de llamada segura (`?.`)**: El operador de llamada segura `?.` te permite acceder a las propiedades de un objeto nulo sin lanzar una excepción de referencia nula.

- **Operador de elvis (`?:`)**: El operador de elvis `?:` te permite proporcionar un valor predeterminado en caso de que una expresión sea nula.

- **Operador de no nulo (`!!`)**: El operador de no nulo `!!` te permite forzar la ejecución de una expresión nula, lo que puede lanzar una excepción de referencia nula si la expresión es nula.

```kotlin
val nombre: String? = null

val longitud = nombre?.length // null

val longitud = nombre?.length ?: 0 // 0

val longitud = nombre!!.length // Lanza una excepción de referencia nula
```

En el ejemplo anterior, la variable `nombre` es de tipo `String?` nulo, por lo que puede contener un valor nulo.

- En la primera línea, se utiliza el operador de llamada segura `?.` para acceder a la propiedad `length` de la variable `nombre`. Como `nombre` es nulo, la expresión `nombre?.length` devuelve `null`.

- En la segunda línea, se utiliza el operador de elvis `?:` para proporcionar un valor predeterminado de `0` en caso de que la expresión `nombre?.length` sea nula. Como `nombre` es nulo, la expresión `nombre?.length ?: 0` devuelve `0`.

- En la tercera línea, se utiliza el operador de no nulo `!!` para forzar la ejecución de la expresión `nombre!!.length`. Como `nombre` es nulo, la expresión `nombre!!.length` lanza una excepción de referencia nula.

## Funciones de extensión de seguridad de nulos

En Kotlin, puedes utilizar funciones de extensión para agregar funcionalidades a los tipos de datos nulos.

```kotlin
fun String?.oLongitud(): Int {
    return this?.length ?: 0
}

val nombre: String? = null

val longitud = nombre.oLongitud() // 0
```

En el ejemplo anterior, se define una función de extensión `oLongitud` para el tipo de dato `String?`. La función `oLongitud` devuelve la longitud de la cadena si no es nula, o `0` si es nula.

## Conclusiones

El manejo de nulos en Kotlin es una parte fundamental del lenguaje que te permite expresar de forma segura si un valor puede ser nulo o no.

Al utilizar tipos de datos nulos y operadores de seguridad de nulos, puedes prevenir los errores de referencia nula y escribir código más robusto y seguro.

:::tip[Recursos]
- [Documentación oficial de Kotlin sobre seguridad de nulos](https://kotlinlang.org/docs/null-safety.html)
- [Kotlin Playground](https://play.kotlinlang.org/): Un entorno de programación en línea para probar y aprender Kotlin.
- [Apuntes Kotlin](https://github.com/resuadam2/kotlin-apuntes): Un repositorio con apuntes y ejemplos de Kotlin.
:::

