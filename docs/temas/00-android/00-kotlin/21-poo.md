import CodeBlock from '@theme/CodeBlock';

# Programación orientada a objetos en Kotlin

La programación orientada a objetos (POO) es un paradigma de programación que se basa en el concepto de "objetos", que pueden contener datos en forma de campos (también conocidos como atributos) y código en forma de procedimientos (también conocidos como métodos).    

En Kotlin, puedes crear clases y objetos para modelar entidades del mundo real y encapsular datos y comportamientos relacionados. Kotlin es un lenguaje de programación orientado a objetos y admite todas las características tradicionales de la programación orientada a objetos, como la herencia, el polimorfismo, la encapsulación y la abstracción.  

## Clases y objetos

En Kotlin, puedes definir una clase utilizando la palabra clave `class` seguida del nombre de la clase y el cuerpo de la clase entre llaves `{}`. 

```kotlin
class Persona {
    var nombre: String = ""
    var edad: Int = 0
}
```

En el ejemplo anterior, se define una clase `Persona` con dos propiedades `nombre` y `edad`. Las propiedades de la clase se inicializan con valores predeterminados.

Para crear un objeto de una clase en Kotlin, puedes utilizar la palabra clave `val` o `var` seguida del nombre del objeto, el operador de asignación `=` y la invocación del constructor de la clase.

```kotlin
val persona = Persona()
persona.nombre = "Juan"
persona.edad = 30
```

En el ejemplo anterior, se crea un objeto `persona` de la clase `Persona` y se inicializan las propiedades `nombre` y `edad` del objeto.

## Propiedades y métodos

En Kotlin, puedes definir propiedades y métodos en una clase utilizando la palabra clave `var` o `val` seguida del nombre de la propiedad o el método y el tipo de dato de la propiedad o el método.

```kotlin
class Persona {
    var nombre: String = ""
    var edad: Int = 0

    fun saludar() {
        println("Hola, soy $nombre")
    }
}
```

En el ejemplo anterior, se define una clase `Persona` con dos propiedades `nombre` y `edad`, y un método `saludar` que imprime un mensaje de saludo con el nombre de la persona.

Para acceder a las propiedades y métodos de un objeto en Kotlin, puedes utilizar el operador de acceso `.` seguido del nombre de la propiedad o el método.

```kotlin
val persona = Persona()
persona.nombre = "Juan"
persona.edad = 30
persona.saludar()
```

En el ejemplo anterior, se crea un objeto `persona` de la clase `Persona` y se inicializan las propiedades `nombre` y `edad` del objeto. Luego, se llama al método `saludar` en el objeto `persona` para imprimir un mensaje de saludo con el nombre de la persona.

:::info[Diferencias con Java]
A diferencia de Java, en Kotlin, las propiedades y los métodos de una clase son públicos por defecto, lo que significa que se pueden acceder desde cualquier parte del código.
:::

## Encapsulación

En Kotlin, puedes encapsular propiedades y métodos en una clase utilizando los modificadores de acceso `public`, `protected`, `private` y `internal`.

- `public`: Las propiedades y métodos públicos son accesibles desde cualquier parte del código.
- `protected`: Las propiedades y métodos protegidos son accesibles desde la clase actual y las clases derivadas.
- `private`: Las propiedades y métodos privados son accesibles solo desde la clase actual.
- `internal`: Las propiedades y métodos internos son accesibles desde el módulo actual.

```kotlin
class Persona {
    var nombre: String = ""
        private set
    var edad: Int = 0
        private set

    fun saludar() {
        println("Hola, soy $nombre")
    }
}
```

En el ejemplo anterior, se define una clase `Persona` con dos propiedades `nombre` y `edad` que se han encapsulado con el modificador de acceso `private`. Esto significa que las propiedades `nombre` y `edad` solo se pueden acceder y modificar dentro de la clase `Persona`.

Para acceder a las propiedades de una clase en Kotlin, puedes utilizar los métodos de acceso `get` y `set` para obtener y establecer el valor de una propiedad.

```kotlin
val persona = Persona()
persona.nombre = "Juan"
persona.edad = 30
persona.saludar()
```

En el ejemplo anterior, se crea un objeto `persona` de la clase `Persona` y se inicializan las propiedades `nombre` y `edad` del objeto. 

Sin embargo, al intentar modificar las propiedades `nombre` y `edad` desde fuera de la clase `Persona`, se produce un error de compilación debido a que las propiedades están encapsuladas con el modificador de acceso `private`.

### Setters y Getters personalizados

En Kotlin, puedes definir setters y getters personalizados para las propiedades de una clase utilizando la palabra clave `set` y `get` seguida de la lógica personalizada para establecer y obtener el valor de la propiedad.

```kotlin
class Persona {
    var nombre: String = ""
        set(value) {
            field = value.capitalize()
        }
    var edad: Int = 0
        set(value) {
            field = if (value >= 0) value else 0
        }

    fun saludar() {
        println("Hola, soy $nombre")
    }
}
```

En el ejemplo anterior, se define una clase `Persona` con dos propiedades `nombre` y `edad` que tienen setters personalizados. El setter de la propiedad `nombre` capitaliza el valor de la propiedad, y el setter de la propiedad `edad` establece el valor de la propiedad en `0` si es menor que `0`.

Para acceder a las propiedades de una clase en Kotlin, puedes utilizar los métodos de acceso `get` y `set` para obtener y establecer el valor de una propiedad.

```kotlin
val persona = Persona()
persona.nombre = "juan"
persona.edad = -10
persona.saludar()
```

En el ejemplo anterior, se crea un objeto `persona` de la clase `Persona` y se inicializan las propiedades `nombre` y `edad` del objeto.

## Constructores

En Kotlin, puedes definir un constructor primario utilizando la palabra clave `constructor` seguida de los parámetros del constructor. 

Sin embargo, en Kotlin, puedes omitir la palabra clave `constructor` y definir los parámetros del constructor directamente en la declaración de la clase.

```kotlin
class Persona(nombre: String, edad: Int) {
    var nombre: String = nombre
    var edad: Int = edad
}
```

En el ejemplo anterior, se define una clase `Persona` con un constructor primario que toma dos parámetros `nombre` y `edad`. Las propiedades de la clase se inicializan con los valores de los parámetros del constructor.

Para crear un objeto de una clase con un constructor primario en Kotlin, puedes utilizar la palabra clave `val` o `var` seguida del nombre del objeto, el operador de asignación `=` y la invocación del constructor de la clase con los argumentos del constructor.

```kotlin
val persona = Persona("Juan", 30)
```

En el ejemplo anterior, se crea un objeto `persona` de la clase `Persona` utilizando el constructor primario y se inicializan las propiedades `nombre` y `edad` del objeto.

### Constructores secundarios

En Kotlin, puedes definir constructores secundarios utilizando la palabra clave `constructor` seguida de los parámetros del constructor. 

```kotlin
class Persona {
    var nombre: String = ""
    var edad: Int = 0

    constructor(nombre: String, edad: Int) {
        this.nombre = nombre
        this.edad = edad
    }
}
```

En el ejemplo anterior, se define una clase `Persona` con un constructor secundario que toma dos parámetros `nombre` y `edad`. Las propiedades de la clase se inicializan con los valores de los parámetros del constructor secundario.

Para crear un objeto de una clase con un constructor secundario en Kotlin, puedes utilizar la palabra clave `val` o `var` seguida del nombre del objeto, el operador de asignación `=` y la invocación del constructor secundario de la clase con los argumentos del constructor.

```kotlin
val persona = Persona("Juan", 30)
```

En el ejemplo anterior, se crea un objeto `persona` de la clase `Persona` utilizando el constructor secundario y se inicializan las propiedades `nombre` y `edad` del objeto.

## Herencia

En Kotlin, puedes crear una clase que herede de otra clase utilizando la palabra clave `:` seguida del nombre de la clase base.

```kotlin
open class Persona {
    var nombre: String = ""
    var edad: Int = 0
}

class Empleado : Persona() {
    var salario: Double = 0.0
}
```

En el ejemplo anterior, se define una clase `Persona` con dos propiedades `nombre` y `edad`, y una clase `Empleado` que hereda de la clase `Persona` y agrega una propiedad `salario`.

Para crear un objeto de una clase derivada en Kotlin, puedes utilizar la palabra clave `val` o `var` seguida del nombre del objeto, el operador de asignación `=` y la invocación del constructor de la clase derivada.

```kotlin
val empleado = Empleado()
empleado.nombre = "Juan"
empleado.edad = 30
empleado.salario = 1000.0
```

En el ejemplo anterior, se crea un objeto `empleado` de la clase `Empleado` y se inicializan las propiedades `nombre`, `edad` y `salario` del objeto.

## Polimorfismo

En Kotlin, puedes utilizar el polimorfismo para tratar un objeto de una clase derivada como un objeto de la clase base.

```kotlin
open class Persona {
    open fun saludar() {
        println("Hola, soy una persona")
    }
}

class Empleado : Persona() {
    override fun saludar() {
        println("Hola, soy un empleado")
    }
}
```

En el ejemplo anterior, se define una clase `Persona` con un método `saludar` y una clase `Empleado` que hereda de la clase `Persona` y sobrescribe el método `saludar`.

:::tip[La palabra reservada open]
La palabra reservada `open` se utiliza para marcar una clase o un miembro de una clase como "sobreseíble", lo que significa que puede ser heredado y sobrescrito por clases derivadas. 
:::

Para utilizar el polimorfismo en Kotlin, puedes crear un objeto de la clase derivada y asignarlo a una variable de la clase base.

```kotlin
val persona: Persona = Empleado()
persona.saludar()
```

En el ejemplo anterior, se crea un objeto `empleado` de la clase `Empleado` y se asigna a una variable `persona` de la clase `Persona`. Al llamar al método `saludar` en la variable `persona`, se ejecuta la implementación del método `saludar` de la clase `Empleado`.

## Abstracción

En Kotlin, puedes utilizar la abstracción para definir una clase base con métodos abstractos que deben ser implementados por las clases derivadas.

```kotlin
abstract class Persona {
    abstract fun saludar()
}

class Empleado : Persona() {
    override fun saludar() {
        println("Hola, soy un empleado")
    }
}
```

En el ejemplo anterior, se define una clase `Persona` con un método abstracto `saludar` y una clase `Empleado` que hereda de la clase `Persona` e implementa el método `saludar`.

Para utilizar la abstracción en Kotlin, puedes crear un objeto de la clase derivada y asignarlo a una variable de la clase base.

```kotlin
val persona: Persona = Empleado()
persona.saludar()
```

En el ejemplo anterior, se crea un objeto `empleado` de la clase `Empleado` y se asigna a una variable `persona` de la clase `Persona`. Al llamar al método `saludar` en la variable `persona`, se ejecuta la implementación del método `saludar` de la clase `Empleado`.

