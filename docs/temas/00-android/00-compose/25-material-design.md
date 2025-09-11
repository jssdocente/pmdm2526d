import CodeBlock from '@theme/CodeBlock';

# Material Design en Jetpack Compose

Material Design es un sistema de diseño desarrollado por Google que ayuda a los desarrolladores a crear interfaces de usuario atractivas y coherentes en aplicaciones Android. Con Jetpack Compose, puedes implementar los principios de Material Design de forma sencilla y eficiente.

## Temas y estilos

Jetpack Compose incluye un conjunto de widgets y estilos basados en Material Design que puedes utilizar para crear interfaces de usuario modernas y atractivas. Puedes personalizar los colores, tipografías y formas de tus componentes para adaptarlos a la identidad visual de tu aplicación.

```kotlin
@Composable
fun MiApp() {
    MaterialTheme {
        Column {
            Text(text = "Hola, mundo!", style = MaterialTheme.typography.title-large)
            Button(onClick = { /* Acción */ }) {
                Text(text = "Haz clic aquí")
            }
        }
    }
}
```

En el ejemplo anterior, se define un componente `MiApp` que utiliza `MaterialTheme` para aplicar los estilos de Material Design a los componentes de la interfaz de usuario. Se utiliza `MaterialTheme.typography.title-large` para aplicar un estilo de tipografía grande al texto y se utiliza `Button` para mostrar un botón con el texto "Haz clic aquí".

## Widgets de Material Design

Jetpack Compose incluye una amplia variedad de widgets de Material Design que puedes utilizar para crear interfaces de usuario complejas y atractivas. Algunos de los widgets más comunes son:

- `Button`: Un botón interactivo que el usuario puede pulsar para realizar una acción.
- `TextField`: Un campo de texto que el usuario puede utilizar para introducir texto.
- `Checkbox`: Una casilla de verificación que el usuario puede marcar o desmarcar.
- `RadioButton`: Un botón de opción que el usuario puede seleccionar entre varias opciones.
- `Switch`: Un interruptor que el usuario puede activar o desactivar.

Puedes utilizar estos widgets y muchos más para crear interfaces de usuario modernas y atractivas en tus aplicaciones Android con Jetpack Compose.

## Personalización de estilos

Puedes personalizar los estilos de Material Design en Jetpack Compose para adaptarlos a la identidad visual de tu aplicación. Puedes definir tus propios temas y estilos utilizando el componente `MaterialTheme` y los objetos `Typography`, `Colors` y `Shapes`.

```kotlin
val MiTema = lightColors(
    primary = Color(0xFF6200EE),
    primaryVariant = Color(0xFF3700B3),
    secondary = Color(0xFF03DAC6)
)

@Composable
fun MiApp() {
    MaterialTheme(colors = MiTema) {
        Column {
            Text(text = "Hola, mundo!", style = MaterialTheme.typography.title-large)
            Button(onClick = { /* Acción */ }) {
                Text(text = "Haz clic aquí")
            }
        }
    }
}
```

En el ejemplo anterior, se define un tema personalizado `MiTema` con colores personalizados y se utiliza `MaterialTheme` para aplicar este tema a los componentes de la interfaz de usuario. Se utiliza `MaterialTheme.typography.title-large` para aplicar un estilo de tipografía grande al texto y se utiliza `Button` para mostrar un botón con el texto "Haz clic aquí".

## Recursos

- [Documentación oficial de Material Design](https://material.io/design): La documentación oficial de Material Design, que incluye guías, tutoriales y ejemplos para aprender a diseñar interfaces de usuario con Material Design.

- [Documentación oficial de Jetpack Compose](https://developer.android.com/jetpack/compose?hl=es-419): La documentación oficial de Jetpack Compose, que incluye guías, tutoriales y ejemplos para aprender a crear interfaces de usuario con Compose.

