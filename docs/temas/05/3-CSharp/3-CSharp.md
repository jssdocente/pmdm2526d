# El lenguaje C Sharp

C# es un lenguaje de programación orientado a objetos desarrollado por Microsoft. 

Es un lenguaje de programación moderno y seguro que se utiliza para crear aplicaciones de escritorio, aplicaciones web y aplicaciones móviles. 

C# es un lenguaje de programación de alto nivel que se utiliza para crear aplicaciones de software de alta calidad.

## Sintaxis básica

La sintaxis de C# es similar a la de otros lenguajes de programación como Java y C++.

```csharp
using System;

namespace HelloWorld
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

## Tipos de datos en C#

C# es un lenguaje de programación fuertemente tipado, lo que significa que cada variable debe tener un tipo de datos específico.    

Los tipos de datos en C# se dividen en dos categorías: tipos de datos primitivos y tipos de datos de referencia.

### Tipos de datos primitivos

Los tipos de datos primitivos en C# son los siguientes:

- `int`: Representa un número entero.
- `float`: Representa un número de punto flotante de precisión simple.
- `double`: Representa un número de punto flotante de precisión doble.
- `char`: Representa un carácter Unicode.
- `bool`: Representa un valor booleano (verdadero o falso).

### Tipos de datos de referencia

Los tipos de datos de referencia en C# son los siguientes:

- `string`: Representa una secuencia de caracteres.
- `object`: Representa cualquier tipo de datos.
- `array`: Representa una colección de elementos del mismo tipo.

## Estructuras de control

Las estructuras de control en C# permiten controlar el flujo de ejecución de un programa.

### Estructuras de control condicionales

Las estructuras de control condicionales en C# son las siguientes:

- `if`: Permite ejecutar un bloque de código si se cumple una condición.
- `else`: Permite ejecutar un bloque de código si no se cumple la condición del `if`.
- `else if`: Permite evaluar una condición adicional si no se cumple la condición del `if`.
- `switch`: Permite evaluar múltiples condiciones y ejecutar un bloque de código en función del valor de una expresión.

### Estructuras de control iterativas

Las estructuras de control iterativas en C# son las siguientes:

- `for`: Permite ejecutar un bloque de código un número específico de veces.
- `while`: Permite ejecutar un bloque de código mientras se cumpla una condición.
- `do while`: Permite ejecutar un bloque de código al menos una vez y luego repetirlo mientras se cumpla una condición.

## Funciones en C#

Las funciones en C# se utilizan para encapsular un bloque de código que realiza una tarea específica.

```csharp
using System;

namespace HelloWorld
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine(Suma(5, 3));
        }

        static int Suma(int a, int b)
        {
            return a + b;
        }
    }
}
```

## Clases y objetos en C#

Las clases y objetos en C# se utilizan para modelar entidades del mundo real.

```csharp
using System;

namespace HelloWorld
{
    class Program
    {
        static void Main(string[] args)
        {
            Persona persona = new Persona("Juan", 25);
            Console.WriteLine(persona.Nombre);
            Console.WriteLine(persona.Edad);
        }
    }

    class Persona
    {
        public string Nombre { get; set; }
        public int Edad { get; set; }

        public Persona(string nombre, int edad)
        {
            Nombre = nombre;
            Edad = edad;
        }
    }
}
```


