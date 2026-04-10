# **Scripting en Unity**

El **Script** es un componente que se utiliza para añadir comportamiento programado a un GameObject en Unity.

Los scripts en Unity se escriben en C# y se utilizan para controlar el comportamiento de los objetos en la escena, como el movimiento, la interacción, la lógica del juego, etc.

La programación en Unity es otro de los pilares del desarrollo de videojuegos que es un universo en sí mismo, seguramente uno de los más complejos y extensos. En Unity actualmente se programa usando el lenguaje C#. Este lenguaje es sintácticamente bastante parecido a Java, completamente orientado a Objetos, lo cuál facilita mucho la interacción con los distintos GameObjects, Componentes y Propiedades de los elementos anteriormente citados.

Aunque se podría aprender C# de forma independiente a Unity, debemos tener en cuenta que con lo que principalmente se trabajará en los Scripts que utilicemos junto con en Unity serán las librerías, clases y funciones propias del motor. 

## **Creación de un Script**

Empezaremos por crear un  archivo de código, es decir, un Script de C#. Dentro de los proyectos de UNity, los Scripts funcionan como un Asset más que podemos agregar a nuestra pestaña de proyecto. Para ello, desde la barra de herramientas (por ejemplo), seleccionaremos **Assets → Create → C# Script**. Lo ideal sería almacenar los scripts en una carpeta llamada Scripts. 

La existencia del Script por si solo en el proyecto no hace que el código se ejecute, al igual que con el resto de Assets, para poder usarlos debemos agregarlos a un objeto de la escena y esto, en el caso de los Scripts se hará en forma de Componente. Para editar el código del Script podemos hacer doble clic sobre él o bien pulsar en el botón de Open en el inspector teniéndolo seleccionado, esto nos abrirá el editor de código que tengamos asignado por defecto será Visual Studio de Microsoft (el cuál se nos habrá instalado con la instalación de Unity o nos habrá pedido que lo instalemos dependiendo de la versión del motor y del editor de código), pero también podemos usar un editor de nuestra preferencia. No confundir Visual Studio con Visual Studio Code, ambos editores son de Microsoft pero no son el mismo producto, aunque si podemos usar ambos indistintamente. 

:::tip[Cambiar el editor de código por defecto]
Para cambiar el editor de código por defecto en Unity, debemos ir a **Edit → Preferences → External Tools** y seleccionar el editor de código que queramos usar.
:::

Veamos ahora el esquema general de un script y algunos de los términos fundamentales de la programación de Scripts en Unity. Por defecto, cuándo creamos un Script se nos genera ya con unas pequeñas líneas de código, es decir, con una estructura básica, que son las siguientes:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class NombreDelScript : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        
    }
}
```

- **using**: Es una directiva que se utiliza para importar librerías de C# que necesitemos en nuestro Script. En este caso, estamos importando las librerías `System.Collections`, `System.Collections.Generic` y `UnityEngine`. La librería `UnityEngine` es la que nos permite acceder a las clases y funciones propias de Unity. 
- **public class NombreDelScript : MonoBehaviour**: Es la definición de la clase del Script. La clase del Script debe heredar de `MonoBehaviour` para que pueda ser un componente de un GameObject en Unity. El nombre de la clase debe ser el mismo que el nombre del archivo del Script.
- **void Start()**: Es un método que se llama una vez al inicio del juego, antes de que se ejecute el primer frame. Se utiliza para inicializar variables y configurar el estado inicial del objeto.    
- **void Update()**: Es un método que se llama una vez por frame. Se utiliza para actualizar el comportamiento del objeto en cada frame, como el movimiento, la interacción, la lógica del juego, etc.  

Como en cualquier lenguaje, a medida que avancemos haciendo pruebas, ejercicios y demás, iremos conociendo otros namespaces necesarios para interactuar con distintos elementos del motor, como puede ser el namespace UI de UnityEngine o el SceneManagement, para interactuar con la UI y con el manejo de las escenas respectivamente, todas las librerías y clases disponibles podemos consultarlas en la documentación oficial. También es posible agregar namespace externos, cuándo instalamos algún asset de alguna tienda o alguna librería que encontremos por internet. 

Los nombres de las clases deben respetar el CamelCase y su nombre debe coincidir con el nombre del archivo creado. Esto Unity ya lo hace de forma automática. Debemos intentar hacer los nombres lo más representativos posible.

Cómo podemos observar, la herencia en C# se realiza a través de los dos puntos (“:”). Por defecto todas las clases heredan de una llamada MonoBehaviour. Esta es la clase principal en Unity y es la que nos permite utilizar funciones preestablecidas. Se puede no heredar de ella si no necesitamos estas funciones. Por supuesto, una clase hija de otra también hereda todo de lo que heredó la clase padre. 

En el caso de los métodos Start y Update, estos son métodos predefinidos que se ejecutan en un orden concreto. Start se ejecuta una vez al inicio del juego, antes de que se ejecute el primer frame, y Update se ejecuta una vez por frame.

## **Variables y tipos de datos**

Las variables en C# son contenedores que se utilizan para almacenar datos. En C# existen varios tipos de datos que se pueden utilizar para declarar variables, como:

- **int**: Para almacenar números enteros.
- **float**: Para almacenar números decimales.
- **string**: Para almacenar cadenas de texto.
- **bool**: Para almacenar valores booleanos (verdadero o falso).
- **Vector3**: Para almacenar vectores en 3D.
- **GameObject**: Para almacenar referencias a GameObjects en la escena.
- **Transform**: Para almacenar referencias a las transformaciones de los GameObjects.

Para declarar una variable en C#, se utiliza la siguiente sintaxis:

```csharp
tipoDeDato nombreDeVariable = valor;
```

Por ejemplo, para declarar una variable de tipo entero llamada `numero` con un valor de `10`, se haría de la siguiente manera:

```csharp
int numero = 10;
```

Las variables en C# son sensibles a mayúsculas y minúsculas, por lo que `numero` y `Numero` serían dos variables diferentes.

## Funciones y métodos

Las funciones en C# se utilizan para agrupar un conjunto de instrucciones que se pueden llamar desde cualquier parte del código. En Unity, las funciones se utilizan para definir el comportamiento de los objetos en la escena.

Para declarar una función en C#, se utiliza la siguiente sintaxis:

```csharp
tipoDeDato nombreDeFuncion(parametros)
{
    // Código de la función
}
```

Por ejemplo, para declarar una función de tipo `void` llamada `Saludar` que imprime un mensaje por consola, se haría de la siguiente manera:

```csharp
void Saludar()
{
    Debug.Log("¡Hola, mundo!");
}
```

Para llamar a una función desde otra parte del código, se utiliza el nombre de la función seguido de paréntesis y punto y coma:

```csharp
Saludar();
```

## **Las funciones del MonoBehaviour**

En Unity, las funciones del MonoBehaviour son funciones predefinidas que se utilizan para controlar el comportamiento de los objetos en la escena. Algunas de las funciones más comunes del MonoBehaviour son:  

- **Awake()**: Se llama una vez al inicio del juego, antes de que se ejecute el primer frame. Se utiliza para inicializar variables y configurar el estado inicial del objeto.
- **Start()**: Se llama una vez al inicio del juego, antes de que se ejecute el primer frame.
- **Update()**: Se llama una vez por frame. Se utiliza para actualizar el comportamiento del objeto en cada frame. 
- **FixedUpdate()**: Se llama una vez por frame, pero se sincroniza con el tiempo físico del juego. Se utiliza para actualizar el comportamiento del objeto en cada frame, pero con una frecuencia fija.
- **LateUpdate()**: Se llama una vez por frame, después de que se hayan procesado todos los Updates. Se utiliza para actualizar el comportamiento del objeto después de que se hayan procesado todos los Updates.
- **OnEnable()**: Se llama cuando el objeto se activa.
- **OnDisable()**: Se llama cuando el objeto se desactiva.
- **OnDestroy()**: Se llama cuando el objeto se destruye.


Estas funciones se pueden sobrescribir en un Script para personalizar el comportamiento del objeto en la escena. Por ejemplo, para sobrescribir la función `Start()` en un Script, se haría de la siguiente manera:

```csharp
void Start()
{
    Debug.Log("¡Hola, mundo!");
}
```

La función **Awake** se ejecuta una única vez cuándo el objeto se indexa en la escena. Tras esto, se ejecuta una única vez la función **Start**, inmediatamente después del Awake, en cuanto el GameObject ya está activo y listo. Por último, tenemos la función **Update**, la cuál se ejecuta tantas veces por segundo como frames por segundo pueda ejecutar el hardware dónde se ejecuta el juego. 

Es importante entender el concepto de **FPS** o **Frames per Seconds**, es decir, fotogramas por segundo. Si un juego se está ejecutando a 30 FPS, quiere decir que visualiza 30 frames por pantalla en cada segundo, y, en lo que a nosotros respecta a nivel programación, esto quiere decir que el método Update se va a lanzar 30 veces en cada segundo. 

Los FPS no solo van a variar dependiendo del hardware en dónde se ejecute nuestro juego, si no que además también variarán dentro de un mismo dispositivo dependiendo de la carga de trabajo que tenga la máquina en cada momento. Es importante programar el método Update tratando de que le juego funcione de la misma forma independientemente de que vaya a más o menos FPS, siempre dentro de unos límites comprensibles.

Otras funciones relevantes, entre otras, son las siguientes:
 
- **OnTriggerEnter(Collider other)**: Se llama cuando un objeto entra en el trigger del objeto.
- **OnTriggerExit(Collider other)**: Se llama cuando un objeto sale del trigger del objeto.
- **OnCollisionEnter(Collision collision)**: Se llama cuando un objeto colisiona con otro objeto.
- **OnCollisionExit(Collision collision)**: Se llama cuando un objeto deja de colisionar con otro objeto.
- **OnMouseDown()**: Se llama cuando se hace clic en el objeto con el ratón.
- **OnMouseEnter()**: Se llama cuando el ratón entra en el objeto.
- **OnMouseExit()**: Se llama cuando el ratón sale del objeto.
- **OnMouseOver()**: Se llama cuando el ratón está sobre el objeto.
- **OnMouseUp()**: Se llama cuando se suelta el clic del ratón en el objeto.

**[Documentación oficial sobre el MonoBehaviour](https://docs.unity3d.com/ScriptReference/MonoBehaviour.html)
**

A la hora de probar el código es importante tener en cuenta que deberemos hacerlo pulsando en el Play de la escena para poder ejecutarla y que también tenemos disponible en la ventana dónde se aloja la pestaña de Project, otra pestaña con la consola y la salida estándar en ella para debuguear, para ello nos será muy útil la clase Log. 

Con la clase Log podemos imprimir mensajes en la consola de Unity, para ello usaremos el método Log de la clase Debug. 

```csharp
Debug.Log("Mensaje de depuración");
```

### Ejemplo de uso de un Script

Vamos a ver un ejemplo sencillo de cómo utilizar un Script en Unity. Para ello, vamos a crear un Script que haga que un cubo rote en la escena.

1. Crearemos un nuevo proyecto en Unity y crearemos un nuevo GameObject 3D en la escena. Para ello, haremos clic en **GameObject → 3D Object → Cube**.
2. Seleccionaremos el cubo en la jerarquía y añadiremos un componente **Rigidbody**. Para ello, haremos clic en **Add Component → Physics → Rigidbody**.
3. Crearemos un nuevo Script de C# en la pestaña de proyecto. Para ello, haremos clic en **Assets → Create → C# Script** y lo llamaremos `RotarCubo`.
4. Editaremos el Script `RotarCubo` y escribiremos el siguiente código:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RotarCubo : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        transform.Rotate(new Vector3(0, 1, 0));
    }
}
```

5. Asociaremos el Script `RotarCubo` al cubo en la escena. Para ello, arrastraremos el Script desde la pestaña de proyecto al cubo en la jerarquía.

Una vez hecho esto, si ejecutamos la escena, veremos cómo el cubo rota en la escena. Esto es debido a que hemos asociado el Script `RotarCubo` al cubo y hemos escrito un código en el Script que hace que el cubo rote en cada frame.  

Este es un ejemplo muy sencillo de cómo utilizar un Script en Unity. A partir de aquí, podemos crear Scripts más complejos con comportamientos más avanzados para los objetos en la escena. 

En resumen, los Scripts en Unity son una parte fundamental del desarrollo de videojuegos y aplicaciones interactivas. Con los Scripts, podemos controlar el comportamiento de los objetos en la escena y crear experiencias interactivas y realistas para los usuarios. 

## El ciclo de vida de un Script

El ciclo de vida de un Script en Unity es el conjunto de eventos que se producen en un Script desde que se carga en la escena hasta que se destruye.

El ciclo de vida de un Script en Unity consta de los siguientes eventos:

- **Awake()**: Se llama una vez al inicio del juego, antes de que se ejecute el primer frame. Se utiliza para inicializar variables y configurar el estado inicial del objeto.
- **OnEnable()**: Se llama cuando el objeto se activa.
- **Start()**: Se llama una vez al inicio del juego, antes de que se ejecute el primer frame.
- **Update()**: Se llama una vez por frame. Se utiliza para actualizar el comportamiento del objeto en cada frame.
- **FixedUpdate()**: Se llama una vez por frame, pero se sincroniza con el tiempo físico del juego.
- **LateUpdate()**: Se llama una vez por frame, después de que se hayan procesado todos los Updates.
- **OnDisable()**: Se llama cuando el objeto se desactiva.
- **OnDestroy()**: Se llama cuando el objeto se destruye.

El ciclo de vida de un Script en Unity es importante para entender cómo se ejecutan las funciones y cómo se controla el comportamiento de los objetos en la escena.

## El Input en Unity

El **Input** en Unity se utiliza para detectar la entrada del usuario, como el teclado, el ratón, el mando, la pantalla táctil, etc.

Para detectar la entrada del usuario en Unity, se utilizan los métodos de la clase `Input`. Algunos de los métodos más comunes de la clase `Input` son:

- **Input.GetKey(KeyCode key)**: Devuelve verdadero si la tecla especificada está presionada.
- **Input.GetKeyDown(KeyCode key)**: Devuelve verdadero si la tecla especificada se ha presionado en este frame.
- **Input.GetKeyUp(KeyCode key)**: Devuelve verdadero si la tecla especificada se ha soltado en este frame.
- **Input.GetMouseButton(int button)**: Devuelve verdadero si el botón del ratón especificado está presionado.
- **Input.GetMouseButtonDown(int button)**: Devuelve verdadero si el botón del ratón especificado se ha presionado en este frame.
- **Input.GetMouseButtonUp(int button)**: Devuelve verdadero si el botón del ratón especificado se ha soltado en este frame.
- **Input.GetAxis(string axisName)**: Devuelve el valor del eje especificado.
- **Input.GetButton(string buttonName)**: Devuelve verdadero si el botón especificado está presionado.
- **Input.GetButtonDown(string buttonName)**: Devuelve verdadero si el botón especificado se ha presionado en este frame.
- **Input.GetButtonUp(string buttonName)**: Devuelve verdadero si el botón especificado se ha soltado en este frame.
- **Input.mousePosition**: Devuelve la posición del ratón en la pantalla.

Los métodos relacionados con el teclado y el ratón utilizan el enumerador `KeyCode` para especificar la tecla o el botón que se va a detectar.

Los métodos relacionados con los ejes utilizan el nombre del eje para especificar el eje que se va a detectar. Los ejes más comunes en Unity son `Horizontal` y `Vertical`. Esto hace referencia a las teclas de dirección del teclado o a los botones del mando que se utilizan para mover un objeto en la escena. 

Los métodos relacionados con los botones utilizan el nombre del botón para especificar el botón que se va a detectar. Los botones más comunes en Unity son `Jump` para saltar y `Fire1` para disparar.

Los métodos relacionados con el ratón utilizan el número del botón para especificar el botón del ratón que se va a detectar. El botón izquierdo del ratón se representa con el número `0`, el botón derecho con el número `1` y el botón central con el número `2`.

Por ejemplo, para detectar si se ha presionado la tecla `Espacio`, se puede utilizar el siguiente código:

```csharp
if (Input.GetKeyDown(KeyCode.Space))
{
    Debug.Log("Se ha presionado la tecla Espacio");
}
```

El Input en Unity es una parte fundamental del desarrollo de videojuegos y aplicaciones interactivas. Con el Input, podemos detectar la entrada del usuario y controlar el comportamiento de los objetos en la escena.  

### Ejemplo de uso del Input

Vamos a ver un ejemplo sencillo de cómo utilizar el Input en Unity. Para ello, vamos a crear un Script que haga que un cubo se mueva en la escena con las teclas de dirección del teclado.  

1. Crearemos un nuevo proyecto en Unity y crearemos un nuevo GameObject 3D en la escena. Para ello, haremos clic en **GameObject → 3D Object → Cube**.
2. Seleccionaremos el cubo en la jerarquía y añadiremos un componente **Rigidbody**. Para ello, haremos clic en **Add Component → Physics → Rigidbody**.
3. Crearemos un nuevo Script de C# en la pestaña de proyecto. Para ello, haremos clic en **Assets → Create → C# Script** y lo llamaremos `MoverCubo`.
4. Editaremos el Script `MoverCubo` y escribiremos el siguiente código:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class MoverCubo : MonoBehaviour
{
    public float velocidad = 5f;

    // Update is called once per frame
    void Update()
    {
        float movimientoHorizontal = Input.GetAxis("Horizontal");
        float movimientoVertical = Input.GetAxis("Vertical");

        Vector3 movimiento = new Vector3(movimientoHorizontal, 0, movimientoVertical);

        transform.Translate(movimiento * velocidad * Time.deltaTime);
    }
}
```

5. Asociaremos el Script `MoverCubo` al cubo en la escena. Para ello, arrastraremos el Script desde la pestaña de proyecto al cubo en la jerarquía.

Una vez hecho esto, si ejecutamos la escena, veremos cómo el cubo se mueve en la escena con las teclas de dirección del teclado. Esto es debido a que hemos asociado el Script `MoverCubo` al cubo y hemos escrito un código en el Script que hace que el cubo se mueva con las teclas de dirección del teclado.    

Este es un ejemplo muy sencillo de cómo utilizar el Input en Unity. A partir de aquí, podemos crear Scripts más complejos con comportamientos más avanzados para los objetos en la escena.  

En resumen, el Input en Unity es una parte fundamental del desarrollo de videojuegos y aplicaciones interactivas. Con el Input, podemos detectar la entrada del usuario y controlar el comportamiento de los objetos en la escena.

:::tip[Sobre Time.deltaTime]
Time.deltaTime es una variable que nos da el tiempo que ha pasado desde el último frame. 
Esto es importante para que el movimiento sea fluido y no dependa de la velocidad de fotogramas del hardware en el que se ejecute el juego.

Si no se multiplica el movimiento por Time.deltaTime, el movimiento del objeto dependerá de la velocidad de fotogramas del hardware en el que se ejecute el juego, lo que puede hacer que el movimiento sea más rápido o más lento en función de la velocidad de fotogramas.
:::

### InputManager

El **InputManager** en Unity es un componente que se utiliza para configurar la entrada del usuario en el juego.

El InputManager en Unity se utiliza para configurar los ejes, los botones y las teclas que se van a utilizar para controlar el comportamiento de los objetos en la escena.

Para acceder al InputManager en Unity, se puede hacer clic en **Edit → Project Settings → InputManager**.

En el InputManager se pueden configurar los ejes, los botones y las teclas que se van a utilizar en el juego. Por ejemplo, se pueden configurar los ejes `Horizontal` y `Vertical` para controlar el movimiento de un objeto en la escena con las teclas de dirección del teclado.  

El InputManager en Unity es una parte fundamental del desarrollo de videojuegos y aplicaciones interactivas. Con el InputManager, podemos configurar la entrada del usuario en el juego y controlar el comportamiento de los objetos en la escena.  

:::tip[Sobre el control con mandos o teclado]
En el InputManager también se pueden configurar los ejes, los botones y las teclas que se van a utilizar para controlar el juego con un mando o un teclado. Por ejemplo, se pueden configurar los ejes `Horizontal` y `Vertical` para controlar el movimiento de un objeto en la escena con un mando o las teclas de dirección del teclado. 
:::

### Entrada táctil

La entrada táctil en Unity se utiliza para detectar la entrada del usuario en dispositivos táctiles, como smartphones y tablets.

Para detectar la entrada táctil en Unity, se utilizan los métodos de la clase `Input`. Algunos de los métodos más comunes de la clase `Input` para la entrada táctil son:

- **Input.touchCount**: Devuelve el número de toques en la pantalla.
- **Input.GetTouch(int index)**: Devuelve el objeto `Touch` en el índice especificado.
- **Touch.phase**: Devuelve la fase del toque (Began, Moved, Stationary, Ended, Canceled).
    - **TouchPhase.Began**: El toque ha comenzado.
    - **TouchPhase.Moved**: El toque se ha movido.
    - **TouchPhase.Stationary**: El toque está estacionario.
    - **TouchPhase.Ended**: El toque ha terminado.
    - **TouchPhase.Canceled**: El toque ha sido cancelado.
- **Touch.position**: Devuelve la posición del toque en la pantalla.
- **Touch.deltaPosition**: Devuelve el desplazamiento del toque en la pantalla.
- **Touch.deltaTime**: Devuelve el tiempo transcurrido desde el último frame.

Por ejemplo, para detectar si se ha tocado la pantalla en un dispositivo táctil, se puede utilizar el siguiente código:

```csharp
if (Input.touchCount > 0)
{
    Touch touch = Input.GetTouch(0);

    if (touch.phase == TouchPhase.Began)
    {
        Debug.Log("Se ha tocado la pantalla");
    }
}
```

La entrada táctil en Unity es una parte fundamental del desarrollo de videojuegos y aplicaciones interactivas para dispositivos táctiles. Con la entrada táctil, podemos detectar la entrada del usuario en dispositivos táctiles y controlar el comportamiento de los objetos en la escena.    

### Input System package

El **Input System package** en Unity es un paquete que se utiliza para gestionar la entrada del usuario en el juego.

El Input System package en Unity se utiliza para configurar los ejes, los botones y las teclas que se van a utilizar para controlar el comportamiento de los objetos en la escena.  

Para acceder al Input System package en Unity, se puede hacer clic en **Window → Package Manager** y buscar el paquete **Input System**.

El Input System package en Unity es una parte fundamental del desarrollo de videojuegos y aplicaciones interactivas. Con el Input System package, podemos configurar la entrada del usuario en el juego y controlar el comportamiento de los objetos en la escena.  

:::tip[Sobre el Input System package]
El Input System package en Unity es una alternativa al InputManager que ofrece más funcionalidades y flexibilidad para gestionar la entrada del usuario en el juego. Con el Input System package, se pueden configurar los ejes, los botones y las teclas de forma más sencilla y eficiente.    
:::


