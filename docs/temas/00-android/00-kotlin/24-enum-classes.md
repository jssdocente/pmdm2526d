import CodeBlock from '@theme/CodeBlock';

# Enum classes en Kotlin

En Kotlin, puedes crear enumeraciones utilizando la palabra clave `enum class`.

Las enumeraciones son una forma de definir un conjunto fijo de constantes que representan valores específicos. Cada constante en una enumeración tiene un nombre y un valor asociado.

```kotlin
enum class Color {
    ROJO, VERDE, AZUL
}
```

En el ejemplo anterior, se define una enumeración `Color` con tres constantes `ROJO`, `VERDE` y `AZUL`.

## Propiedades de las enumeraciones

Las enumeraciones en Kotlin tienen las siguientes propiedades:

- **Constantes**: Las enumeraciones contienen un conjunto fijo de constantes que representan valores específicos.

- **Propiedades y métodos**: Las enumeraciones pueden tener propiedades y métodos asociados con cada constante.

- **Constructores**: Las enumeraciones pueden tener constructores personalizados para inicializar las constantes con valores específicos.

- **Implementación de interfaces**: Las enumeraciones pueden implementar interfaces y heredar de clases.

## Acceso a las constantes de una enumeración

Puedes acceder a las constantes de una enumeración utilizando la notación de punto.

```kotlin
val color = Color.ROJO
```

En el ejemplo anterior, se accede a la constante `ROJO` de la enumeración `Color` y se asigna a la variable `color`.

## Propiedades y métodos de una enumeración

Puedes definir propiedades y métodos en una enumeración para agregar funcionalidades a las constantes.

```kotlin
enum class Color(val rgb: Int) {
    ROJO(0xFF0000),
    VERDE(0x00FF00),
    AZUL(0x0000FF);

    fun nombreEnMayusculas(): String {
        return name.toUpperCase()
    }
}
```

En el ejemplo anterior, se define una enumeración `Color` con una propiedad `rgb` y un método `nombreEnMayusculas` que devuelve el nombre de la constante en mayúsculas.

## Implementación de interfaces con enumeraciones

Las enumeraciones en Kotlin pueden implementar interfaces para agregar funcionalidades a las constantes.

```kotlin
interface Describible {
    fun describir(): String
}

enum class DiaSemana : Describible {
    LUNES {
        override fun describir(): String {
            return "Primer día de la semana"
        }
    },
    MARTES {
        override fun describir(): String {
            return "Segundo día de la semana"
        }
    },
    MIERCOLES {
        override fun describir(): String {
            return "Tercer día de la semana"
        }
    }
}
```

En el ejemplo anterior, se define una interfaz `Describible` con un método `describir`.

Se crea una enumeración `DiaSemana` que implementa la interfaz `Describible` y define la implementación del método `describir` para cada constante.

## Uso de enumeraciones en Kotlin

Las enumeraciones son útiles en Kotlin para:

- Definir un conjunto fijo de constantes que representan valores específicos.
- Agregar propiedades y métodos a las constantes para agregar funcionalidades.
- Implementar interfaces y heredar de clases para agregar funcionalidades a las constantes.
