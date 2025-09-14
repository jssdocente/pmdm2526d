# Ciclo de vida de una aplicación Android

El ciclo de vida de una aplicación Android es el conjunto de estados por los que pasa una aplicación desde que se inicia hasta que se detiene. Comprender el ciclo de vida de una aplicación es fundamental para desarrollar aplicaciones robustas y eficientes en Android. 

## Componentes del ciclo de vida

En Android, los componentes del ciclo de vida de una aplicación son actividades, fragmentos, servicios y receptores de difusión. Cada uno de estos componentes tiene su propio ciclo de vida y se comporta de manera diferente en función de los eventos que se producen en la aplicación.

Vamos a centrarnos en las Actividades o `Activity`, que son los componentes principales de una aplicación Android y representan una única pantalla con la que el usuario puede interactuar. 

## Estados de una actividad

Las actividades en Android pueden estar en uno de los siguientes estados:

- **Created**: La actividad ha sido creada pero aún no es visible para el usuario.
- **Started**: La actividad es visible para el usuario pero no tiene el foco.
- **Resumed**: La actividad está en primer plano y tiene el foco.
- **Paused**: La actividad ha perdido el foco pero aún es visible para el usuario.
- **Stopped**: La actividad ya no es visible para el usuario.
- **Destroyed**: La actividad ha sido destruida y liberada de la memoria.

## Métodos del ciclo de vida

En Android, cada actividad tiene una serie de métodos que se invocan en función de su estado en el ciclo de vida. Algunos de los métodos más comunes son:

- `onCreate()`: Se llama cuando la actividad se crea por primera vez.
- `onStart()`: Se llama cuando la actividad se hace visible para el usuario.
- `onResume()`: Se llama cuando la actividad obtiene el foco y se convierte en activa.
- `onPause()`: Se llama cuando la actividad pierde el foco pero aún es visible.
- `onStop()`: Se llama cuando la actividad ya no es visible para el usuario.
- `onDestroy()`: Se llama cuando la actividad es destruida y liberada de la memoria.

## Ejemplo de ciclo de vida de una actividad

A continuación, se muestra un ejemplo del ciclo de vida de una actividad en Android:

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        Log.d("MainActivity", "onCreate")
    }

    override fun onStart() {
        super.onStart()
        Log.d("MainActivity", "onStart")
    }

    override fun onResume() {
        super.onResume()
        Log.d("MainActivity", "onResume")
    }

    override fun onPause() {
        super.onPause()
        Log.d("MainActivity", "onPause")
    }

    override fun onStop() {
        super.onStop()
        Log.d("MainActivity", "onStop")
    }

    override fun onDestroy() {
        super.onDestroy()
        Log.d("MainActivity", "onDestroy")
    }
}
```

En el ejemplo anterior, se define una actividad `MainActivity` con los métodos del ciclo de vida más comunes. Cada método imprime un mensaje de registro en la consola para indicar en qué estado se encuentra la actividad.

## Imagen del ciclo de vida de una actividad

A continuación, se muestra una imagen que representa el ciclo de vida de una actividad en Android:

![Ciclo de vida de una actividad en Android](https://developer.android.com/guide/components/images/activity_lifecycle.png?hl=es-419)

Cómo podemos observar, el ciclo de vida de una actividad en Android consta de varios estados y transiciones entre ellos. 

Es importante comprender estos estados para gestionar correctamente el ciclo de vida de una aplicación Android.

## Sobre el contexto o `Context`

El contexto o `Context` es un objeto que proporciona información sobre el entorno en el que se ejecuta una aplicación Android. 

El contexto se utiliza para acceder a recursos de la aplicación, iniciar actividades, crear servicios y mucho más.

En Android, el contexto se puede obtener a través de la clase `Context` o de una subclase de `Context`, como `Activity` o `Service`.

## Obtener el contexto en Android

En Android, puedes obtener el contexto de varias formas, dependiendo de la clase en la que te encuentres. Algunas de las formas más comunes de obtener el contexto son:

- `this`: En una actividad, puedes utilizar `this` para hacer referencia al contexto de la actividad.
- `getApplicationContext()`: En una actividad o un servicio, puedes utilizar `getApplicationContext()` para obtener el contexto de la aplicación.
- `getContext()`: En una vista, puedes utilizar `getContext()` para obtener el contexto de la vista.

!!! tip "Contexto en Compose"
    Para obtener el contexto en un entorno de Compose, puedes utilizar el modificador `LocalContext.current`.

## Usos comunes del contexto

Algunos de los usos comunes del contexto en Android son:

- Acceder a recursos de la aplicación, como cadenas, imágenes y estilos.
- Iniciar actividades y servicios.
- Crear notificaciones y alarmas.
- Acceder a la base de datos de la aplicación.

El contexto es fundamental para el desarrollo de aplicaciones Android y te permite interactuar con el sistema operativo y los servicios de la plataforma.

## Toasts en Android

Los toasts son mensajes breves que se muestran en la pantalla durante un corto período de tiempo. En Android, puedes utilizar la clase `Toast` para mostrar toasts en tu aplicación.

A continuación, se muestra un ejemplo de cómo mostrar un toast en Android:

```kotlin
Toast.makeText(this, "Hola, mundo!", Toast.LENGTH_SHORT).show()
```

En el ejemplo anterior, se utiliza `Toast.makeText()` para crear un toast con el mensaje "Hola, mundo!" y `Toast.LENGTH_SHORT` para especificar la duración del toast.

Los toasts son útiles para mostrar mensajes informativos o de confirmación al usuario de forma rápida y sencilla.

## Snackbar en Android

Los snackbar son mensajes breves que se muestran en la parte inferior de la pantalla y permiten al usuario realizar una acción o deshacer una acción.

En Android, puedes utilizar la clase `Snackbar` de la biblioteca de diseño de materiales para mostrar snackbar en tu aplicación.

A continuación, se muestra un ejemplo de cómo mostrar un snackbar en Android:

```kotlin
Snackbar.make(view, "Mensaje de snackbar", Snackbar.LENGTH_SHORT)
    .setAction("Deshacer") {
        // Acción al deshacer
    }
    .show()
```

En el ejemplo anterior, se utiliza `Snackbar.make()` para crear un snackbar con el mensaje "Mensaje de snackbar" y `Snackbar.LENGTH_SHORT` para especificar la duración del snackbar. Se utiliza `setAction()` para añadir un botón de deshacer al snackbar y `show()` para mostrar el snackbar en la pantalla.

Los snackbar son útiles para mostrar mensajes informativos o de confirmación al usuario y permitirle realizar acciones adicionales.

## Diálogos en Android

Los diálogos son ventanas emergentes que se utilizan para mostrar información, solicitar confirmación al usuario o realizar una acción.

En Android, puedes utilizar la clase `AlertDialog` para crear diálogos en tu aplicación.

A continuación, se muestra un ejemplo de cómo mostrar un diálogo en Android:

```kotlin
val builder = AlertDialog.Builder(this)
builder.setTitle("Título del diálogo")
builder.setMessage("Mensaje del diálogo")
builder.setPositiveButton("Aceptar") { dialog, which ->
    // Acción al hacer clic en Aceptar
}
builder.setNegativeButton("Cancelar") { dialog, which ->
    // Acción al hacer clic en Cancelar
}
builder.show()
```

En compose, puedes utilizar el componente `AlertDialog` para crear diálogos de forma declarativa.

Los diálogos son útiles para interactuar con el usuario y solicitar su entrada o confirmación de forma clara y concisa.

