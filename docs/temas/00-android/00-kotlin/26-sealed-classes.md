import CodeBlock from '@theme/CodeBlock';

# Clases selladas en Kotlin

Las clases selladas son un tipo especial de clase en Kotlin que se utilizan para representar un conjunto finito de subclases. 

Las clases selladas son útiles cuando tienes un conjunto limitado de subclases y quieres asegurarte de que todas las subclases se manejan de forma segura en un bloque `when`.

## Declaración de clases selladas

En Kotlin, puedes declarar una clase sellada utilizando la palabra clave `sealed` antes de la palabra clave `class`. 

```kotlin
sealed class Resultado {
    data class Exito(val mensaje: String) : Resultado()
    data class Error(val mensaje: String) : Resultado()
}
```

En el ejemplo anterior, se declara una clase sellada `Resultado` con dos subclases: `Exito` y `Error`. Ambas subclases tienen una propiedad `mensaje` de tipo `String`.

## Uso de clases selladas

Las clases selladas se utilizan principalmente en expresiones `when` para manejar de forma segura todas las subclases. 

```kotlin
fun procesarResultado(resultado: Resultado) {
    when (resultado) {
        is Resultado.Exito -> println("Exito: ${resultado.mensaje}")
        is Resultado.Error -> println("Error: ${resultado.mensaje}")
    }
}
```

En el ejemplo anterior, se define una función `procesarResultado` que toma un parámetro de tipo `Resultado`. Dentro de la expresión `when`, se manejan de forma segura las subclases `Exito` y `Error` de la clase sellada `Resultado`.

Las clases selladas son una forma segura y concisa de manejar un conjunto finito de subclases en Kotlin.

## Ventajas de las clases selladas

- **Seguridad**: Las clases selladas garantizan que todas las subclases se manejen de forma segura en un bloque `when`.
- **Concisión**: Las clases selladas permiten definir un conjunto finito de subclases de forma concisa y legible.
- **Extensibilidad**: Las clases selladas pueden tener subclases anidadas, lo que permite una mayor extensibilidad y modularidad en el código.

Las clases selladas son una característica poderosa de Kotlin que te permite representar de forma segura un conjunto finito de subclases y manejarlas de forma concisa y legible en tu código.

