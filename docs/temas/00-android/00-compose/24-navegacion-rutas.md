import CodeBlock from '@theme/CodeBlock';
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Navegación y rutas en Jetpack Compose

Jetpack Compose es un marco de trabajo moderno para la creación de interfaces de usuario en aplicaciones Android. Con Compose, puedes crear interfaces de usuario de manera declarativa, lo que significa que puedes definir cómo se ve tu aplicación en función del estado de la misma.

## Configuración de la navegación

Para administrar la navegación en Jetpack Compose, necesitas agregar la dependencia de `navigation-compose` a tu archivo `build.gradle` y definir las rutas y destinos de tu aplicación en un archivo de configuración de navegación.

```kotlin title="build.gradle.kts"
dependencies {
    val nav_version = "2.8.5"

    implementation("androidx.navigation:navigation-compose:$nav_version")
}
```

:::info[Video ejemplo de uso de Navegación]
<iframe width="560" height="315" src="https://www.youtube.com/embed/1SwKa-ODia0?si=hMzFLzsVHiwwVcMb" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
:::

:::info[Video ejemplo de Splash Screen personalizada]
<iframe width="560" height="315" src="https://www.youtube.com/embed/oguCwoGqaD0?si=QUUgNkUX_p7I2cVT" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
:::

## Navegación en Jetpack Compose

En Jetpack Compose, puedes implementar la navegación entre pantallas utilizando el componente `NavHost`. `NavHost` es un contenedor que muestra las pantallas de tu aplicación en función de las rutas definidas.

```kotlin
@Composable
fun MyApp() {
    val navController = rememberNavController()

    NavHost(navController = navController, startDestination = "pantalla1") {
        composable("pantalla1") {
            Pantalla1()
        }
        composable("pantalla2") {
            Pantalla2()
        }
    }
}
```

En el ejemplo anterior, se define un componente `MyApp` que utiliza `NavHost` para implementar la navegación entre dos pantallas: `Pantalla1` y `Pantalla2`. `NavHost` toma un `NavController` como argumento y define las rutas de las pantallas utilizando el método `composable`.

## Definición de rutas

Puedes definir las rutas de tu aplicación utilizando el método `composable` de `NavHost`. Cada ruta tiene un nombre único que se utiliza para identificar la pantalla correspondiente.

```kotlin
NavHost(navController = navController, startDestination = "pantalla1") {
    composable("pantalla1") {
        Pantalla1()
    }
    composable("pantalla2") {
        Pantalla2()
    }
}
```

En el ejemplo anterior, se definen dos rutas: `pantalla1` y `pantalla2`, que corresponden a las pantallas `Pantalla1` y `Pantalla2`, respectivamente.

## Navegación entre pantallas

Puedes navegar entre pantallas en Jetpack Compose utilizando el `NavController` y el método `navigate`.

```kotlin
Button(onClick = { navController.navigate("pantalla2") }) {
    Text(text = "Ir a Pantalla 2")
}
```

En el ejemplo anterior, se define un botón que, al hacer clic en él, navega a la pantalla `Pantalla2` utilizando el método `navigate` del `NavController`.

## Paso de datos entre pantallas

Puedes pasar datos entre pantallas en Jetpack Compose utilizando el método `navigate` y el argumento `arguments`.

```kotlin
Button(onClick = {
    navController.navigate("pantalla2") {
        launchSingleTop = true
        popUpTo("pantalla1") { inclusive = true }
        arguments = bundleOf("dato" to "Hola, mundo!")
    }
}) {
    Text(text = "Ir a Pantalla 2")
}
```

En el ejemplo anterior, se define un botón que, al hacer clic en él, navega a la pantalla `Pantalla2` y pasa el dato `"Hola, mundo!"` como argumento.

El argumento ``arguments`` se utiliza para pasar datos entre pantallas. Puedes utilizar `bundleOf` para crear un `Bundle` con los datos que deseas pasar.

## Recuperación de datos en la pantalla de destino

Puedes recuperar los datos pasados como argumentos en la pantalla de destino utilizando el método `currentBackStackEntryAsState` y el argumento `arguments`.

```kotlin
val navBackStackEntry by navController.currentBackStackEntryAsState()
val dato = navBackStackEntry?.arguments?.getString("dato")
```

En el ejemplo anterior, se recupera el dato pasado como argumento en la pantalla `Pantalla2` utilizando el método `currentBackStackEntryAsState` y el argumento `arguments`.

## Navegación entre pantallas usando lambdas

En Jetpack Compose, puedes utilizar lambdas para definir la navegación entre pantallas de forma más concisa.

La definición de estas lambdas puede hacerse en el método `composable` de `NavHost`. Esto nos ayuda a mantener un código más limpio y legible.

```kotlin
NavHost(navController = navController, startDestination = "pantalla1") {
    composable("pantalla1") {
        Pantalla1 {
            navController.navigate("pantalla2")
        }
    }
    composable("pantalla2") {
        Pantalla2 {
            navController.navigate("pantalla1")
        }
    }
}
```

En el ejemplo anterior, se define la navegación entre las pantallas `Pantalla1` y `Pantalla2` utilizando lambdas en el método `composable` de `NavHost`.

De esta forma, al definir la navegación en el propio componente, se mantiene un código más limpio y legible.

Por ejemplo, el composable `Pantalla1` recibe una lambda que navega a la pantalla `Pantalla2`, y el composable `Pantalla2` recibe una lambda que navega a la pantalla `Pantalla1`.

```kotlin
@Composable
fun Pantalla1(onNavigate: () -> Unit) {
    Button(onClick = onNavigate) {
        Text(text = "Ir a Pantalla 2")
    }
}

@Composable
fun Pantalla2(onNavigate: () -> Unit) {
    Button(onClick = onNavigate) {
        Text(text = "Ir a Pantalla 1")
    }
}
```

En los componibles `Pantalla1` y `Pantalla2`, se define un botón que, al hacer clic en él, ejecuta la lambda recibida como argumento, que navega a la pantalla correspondiente.

## Para la definición de los nombres de las pantallas

Es importante definir nombres significativos para las pantallas de tu aplicación. Los nombres de las pantallas deben reflejar claramente el propósito y la funcionalidad de cada pantalla.

Por ejemplo, si tienes una pantalla de inicio y una pantalla de perfil, puedes nombrarlas `pantallaInicio` y `pantallaPerfil`, respectivamente.

```kotlin
NavHost(navController = navController, startDestination = "pantallaInicio") {
    composable("pantallaInicio") {
        PantallaInicio()
    }
    composable("pantallaPerfil") {
        PantallaPerfil()
    }
}
```

Estos nombres pueden definirse en un archivo de constantes o en un objeto de compañero para mantener un código más organizado y legible. Existen varias opciones para definir los nombres de las pantallas, como objetos de compañero, constantes o enumeraciones. 

<Tabs
defaultValue="companion">
<TabItem value="companion" label="Objeto anónimo">
```kotlin
object Rutas {
    const val PANTALLA_INICIO = "pantallaInicio"
    const val PANTALLA_PERFIL = "pantallaPerfil"
}
```
</TabItem>
<TabItem value="constantes" label="Constantes">
```kotlin
const val PANTALLA_INICIO = "pantallaInicio"
const val PANTALLA_PERFIL = "pantallaPerfil"
```
</TabItem>
<TabItem value="enumeraciones" label="Enumeraciones">
```kotlin
enum class Rutas {
    PANTALLA_INICIO,
    PANTALLA_PERFIL
}
```
</TabItem>
<TabItem value="sealed" label="Clases selladas">
```kotlin
sealed class Rutas {
    object PANTALLA_INICIO : Rutas()
    object PANTALLA_PERFIL : Rutas()
}
```
</TabItem>
</Tabs>

En el ejemplo anterior, se definen los nombres de las pantallas `PANTALLA_INICIO` y `PANTALLA_PERFIL` utilizando un objeto anónimo, constantes, enumeraciones y clases selladas.


## Recursos

- [Documentación oficial de Jetpack Compose Navigation](https://developer.android.com/jetpack/compose/navigation?hl=es-419): La documentación oficial de Jetpack Compose Navigation, que incluye guías, tutoriales y ejemplos para aprender a usar la navegación en Compose.

- [Navegación y arquitectura de la app (MVVM) - Codelabs](https://developer.android.com/courses/android-basics-compose/unit-4?hl=es-419)

- Ejercicio navegación - [Enunciado](https://github.com/resuadam2/enunciados-ejemplos-android-24?tab=readme-ov-file#app-de-pruebas-de-navegación) - [Solución](https://github.com/resuadam2/PruebasNavigationCompose)


