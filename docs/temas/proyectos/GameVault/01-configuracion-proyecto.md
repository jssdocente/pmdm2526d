# **1. Configuración de proyecto y primeros pasos**

## 1. Introducción

Esta guía describe paso a paso cómo completar configuración del proyecto de MyGameStore. Incluye dependencias, estructura de carpetas, rutas de navegación, integración en MainActivity, tema con diseño adaptativo (Dimens) y el esqueleto de pantallas.

#### Resumen de lo que incluye V1

- Proyecto base con Jetpack Compose y Material 3
- Navigation 3
- Rutas en `AppRoutes`
- Integración de navegación en `MainActivity`
- Tema y diseño adaptativo: `Dimens` + inyección vía `CompositionLocal`
- Paleta de colores de la app definida en `Color.kt` y `colors.xml`
- Esqueleto de pantallas básicas

---

### 1) Configuración del proyecto y dependencias

Usaremos Version Catalog (archivo `gradle/libs.versions.toml`) para gestionar versiones y coordenadas.

1.1. Edita `gradle/libs.versions.toml` y asegúrate de tener (o añade) estas versiones y librerías mínimas para V1:

```toml
[versions]
agp = "8.13.2"
kotlin = "2.1.21"
coreKtx = "1.17.0"
lifecycleRuntimeKtx = "2.10.0"
activityCompose = "1.12.2"
composeBom = "2024.09.00"
navigationCompose = "2.8.4"
junit = "4.13.2"
junitVersion = "1.3.0"
espressoCore = "3.7.0"

# Navigation-3
nav3Core = "1.0.0"
nav3lifecycleVM = "2.9.4"
kotlinxSerialization = "1.7.3"

[libraries]
androidx-core-ktx = { group = "androidx.core", name = "core-ktx", version.ref = "coreKtx" }
androidx-lifecycle-runtime-ktx = { group = "androidx.lifecycle", name = "lifecycle-runtime-ktx", version.ref = "lifecycleRuntimeKtx" }
androidx-activity-compose = { group = "androidx.activity", name = "activity-compose", version.ref = "activityCompose" }
androidx-compose-bom = { group = "androidx.compose", name = "compose-bom", version.ref = "composeBom" }
androidx-compose-ui = { group = "androidx.compose.ui", name = "ui" }
androidx-compose-ui-graphics = { group = "androidx.compose.ui", name = "ui-graphics" }
androidx-compose-ui-tooling = { group = "androidx.compose.ui", name = "ui-tooling" }
androidx-compose-ui-tooling-preview = { group = "androidx.compose.ui", name = "ui-tooling-preview" }
androidx-compose-ui-test-manifest = { group = "androidx.compose.ui", name = "ui-test-manifest" }
androidx-compose-ui-test-junit4 = { group = "androidx.compose.ui", name = "ui-test-junit4" }
androidx-compose-material3 = { group = "androidx.compose.material3", name = "material3" }

# Nav3: Core Navigation 3 libraries
androidx-navigation3-runtime = { module = "androidx.navigation3:navigation3-runtime", version.ref = "nav3Core" }
androidx-navigation3-ui = { module = "androidx.navigation3:navigation3-ui", version.ref = "nav3Core" }
androidx-lifecycle-viewmodel-navigation3 = { module = "androidx.lifecycle:lifecycle-viewmodel-navigation3", version.ref = "nav3lifecycleVM" }
kotlinx-serialization-json = { module = "org.jetbrains.kotlinx:kotlinx-serialization-json", version.ref = "kotlinxSerialization" }

[plugins]
android-application = { id = "com.android.application", version.ref = "agp" }
kotlin-android = { id = "org.jetbrains.kotlin.android", version.ref = "kotlin" }
kotlin-compose = { id = "org.jetbrains.kotlin.plugin.compose", version.ref = "kotlin" }

kotlin-serialization = { id = "org.jetbrains.kotlin.plugin.serialization", version.ref = "kotlin" }

```

Nota: en este proyecto trabajamos la navegación con un `NavDisplay` propio para entender conceptos base. La dependencia `navigation-compose` puede añadirse ya (para futuras versiones o comparativas), pero en V1 no es estrictamente necesaria.

1.2. Revisa `app/build.gradle.kts` del módulo `app`. Debe incluir Compose y Material 3, más (opcionalmente) Navigation:

```kotlin
plugins {
    alias(libs.plugins.android.application)
    alias(libs.plugins.kotlin.android)
    alias(libs.plugins.kotlin.compose)
    alias(libs.plugins.kotlin.serialization)
}



dependencies {
    // Core Android
    implementation(libs.androidx.core.ktx)
    implementation(libs.androidx.lifecycle.runtime.ktx)

    // Compose base
    implementation(libs.androidx.activity.compose)
    implementation(platform(libs.androidx.compose.bom))
    implementation(libs.androidx.compose.ui)
    implementation(libs.androidx.compose.ui.graphics)
    implementation(libs.androidx.compose.ui.tooling.preview)
    implementation(libs.androidx.compose.material3)

    //Navigation
    implementation(libs.androidx.navigation3.runtime)
    implementation(libs.androidx.navigation3.ui)
    implementation(libs.androidx.lifecycle.viewmodel.navigation3)
    implementation(libs.kotlinx.serialization.json)

}
```

---

### 2) Estructura de carpetas (Clean Architecture — esqueleto completo)

Para que los alumnos visualicen desde el principio la organización, creamos TODAS las carpetas, aunque en V1 varias quedarán vacías. En esta versión sólo utilizaremos activamente `presentation/ui`.

Sugerencia: cuando una carpeta vaya a quedar vacía, crea un archivo `.keep` (vacío) o `README.md` con 1–2 líneas para que el control de versiones conserve el directorio.

```
app/
└─ src/
   ├─ main/
   │  ├─ AndroidManifest.xml
   │  ├─ java/com/pmdm/mygamestore/
   │  │  ├─ MyGameStoreApp.kt
   │  │  ├─ presentation/
   │  │  │  ├─ ui/
   │  │  │  │  ├─ navigation/          (AppRoutes, NavDisplay)
   │  │  │  │  ├─ screens/             (Splash, Login, Register, Home, Library, Profile, Detail)
   │  │  │  │  ├─ components/          (componentes reutilizables)              [vacío en V1]
   │  │  │  │  └─ theme/               (Color, Dimens, Theme, Type)
   │  │  │  └─ viewmodel/              (ViewModels)                             [vacío en V1]
   │  │  ├─ domain/                     (capa de dominio)                        [vacío en V1]
   │  │  │  ├─ model/                  (entidades del dominio)
   │  │  │  ├─ repository/             (contratos de repositorios)
   │  │  │  └─ usecase/                (casos de uso)
   │  │  └─ data/                       (capa de datos)                           [vacío en V1]
   │  │     ├─ remote/
   │  │     │  ├─ api/                 (interfaces Retrofit)
   │  │     │  └─ dto/                 (modelos de red)
   │  │     ├─ local/
   │  │     │  ├─ db/                  (RoomDatabase)
   │  │     │  └─ dao/                 (DAOs)
   │  │     └─ repository/             (impl. de repositorios)
   │  
   ├─ test/java/com/pmdm/mygamestore/        (tests unitarios)
   └─ androidTest/java/com/pmdm/mygamestore/ (tests instrumentados)
```

Objetivo didáctico: presentar capas desde el inicio. En V1 no hay lógica de `domain` ni `data`, pero se dejan listas las carpetas para versiones posteriores.

---

### 3) Rutas de navegación con `AppRoutes`

Definimos un `sealed interface` con todas las rutas de la app. Ventaja: tipos seguros y parámetros claros.

```kotlin
// app/src/main/java/com/pmdm/mygamestore/presentation/ui/navigation/AppRoutes.kt
sealed interface AppRoutes: NavKey {
    @Serializable
    data object Splash : AppRoutes
    @Serializable
    data object Login : AppRoutes
    @Serializable
    data object Register : AppRoutes
    @Serializable
    data object Home : AppRoutes
    @Serializable
    data object Library : AppRoutes
    @Serializable
    data object Profile : AppRoutes
    @Serializable
    data class Detail(val gameId: Int) : AppRoutes
}
```

Concepto clave: `sealed` permite que el compilador conozca todas las variantes y nos fuerce a manejarlas al navegar o renderizar.

---

### 4) Motor de navegación: `Navigation v3`

Navigation v3 simplifica la navegación tratando las rutas como un estado (una lista de objetos). Sus componentes principales son:

*   **`NavKey`**: Interfaz que deben implementar nuestras rutas (como `AppRoutes`).
*   **`NavDisplay`**: El componente de UI que observa el `backStack` y muestra la pantalla correspondiente.
*   **`entryProvider`**: Mapea cada ruta a su pantalla.


!!! info "Video explicación de Navigation 3 en Compose"


    <iframe width="560" height="315" src="https://www.youtube.com/embed/DHoKUZvkfEs" title="Navigation 3 en Jetpack Compose" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<figcaption>
  <img src="res/img/1-config/01.1-navegacionA.png" width="120%" align="center"  alt="">
  <p style="text-align: center; font-style: italic;">Navegación explicada a nivel de estructura usada</p>
</figcaption>


**¿Cómo funcion la navegación?**

- Se define un grafo de navegación que mapea cada ruta a su pantalla. 
- Se define un `NavDisplay` que observa el `backStack` y muestra la pantalla correspondiente.
- En el `NavDisplay` se define un `entryProvider` que mapea, con una `NavKey` (que es la ruta), cada ruta a su pantalla.

El funcionamiento es sencillo:

- El `rememberNavBackStack` es una lista y a su vez un estado que permite recomposición, cada vez que se modifique esta lista.
- Para ir hacía atrás, se utiliza la acción de back, eliminando el último elemento de la lista.
- Para navegar hacia adelante, simplemente se añade la ruta a la lista, y se recompone la pantalla. El NavDisplay tiene un `entryProvider` para cada una de esas rutas, y devuelve un `Composable` que se encarga de mostrar la pantalla correspondiente.

> Por ahora, la navegación es básica, más adelante se incorporarán más funcionalidades.


```kotlin
// app/src/main/java/com/pmdm/mygamestore/presentation/ui/navigation/NavGraph.kt

// Este objeto permitirá a cualquier pantalla acceder al backStack
val LocalNavStack = staticCompositionLocalOf<MutableList<NavKey>> {
    error("No se ha proporcionado el BackStack. Asegúrate de envolver tu contenido en un CompositionLocalProvider.")
}

@Composable
fun AppNavigation() {
    // Gestiona el historial de navegación, comenzando con la pantalla (la que se indique por defecto)
    val backStack = rememberNavBackStack(AppRoutes.Home)

    //Envolvemos la navegación con el BackStack, permitiendo obtener el `backStack` desde el contexto.
    CompositionLocalProvider(LocalNavStack provides backStack) {
        // Configura el sistema de navegación de la aplicación
        NavDisplay(
            // Pasa el historial de navegación
            backStack = backStack,
            // Función para manejar el botón de retroceso
            onBack = { backStack.removeLastOrNull() },
            // Define las rutas y pantallas disponibles
            entryProvider = entryProvider {
                // Pantalla inicial de carga
                entry(AppRoutes.Splash) {
                    SplashScreen()
                }
                // Pantalla de inicio de sesión
                entry(AppRoutes.Login) {
                    LoginScreen()
                }
                // Pantalla de registro de usuario
                entry(AppRoutes.Register) {
                    RegisterScreen()
                }
                // Pantalla principal con catálogo de juegos
                entry(AppRoutes.Home) {
                    HomeScreen()
                }
                // Pantalla de biblioteca personal
                entry(AppRoutes.Library) {
                    LibraryScreen()
                }
                // Pantalla de perfil de usuario
                entry(AppRoutes.Profile) {
                    ProfileScreen()
                }
                // Pantalla de detalles de un juego específico
                entry<AppRoutes.Detail> { route ->
                    DetailScreen(route.gameId)
                }
            }
        )
    }
}

```

> Concepto clave: patrón de “tabla de rutas” para asociar tipos de `AppRoutes` con `@Composable` concretos.

---

### 5) Integración en `MainActivity`

Ahora, llamamos al NavGraph desde `MainActivity`:

```kotlin
// app/src/main/java/com/pmdm/mygamestore/MainActivity.kt
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContent {
            MyGameStoreTheme {
                // Se llama al grafo de navegación.
                AppNavigation()
            }
        }
    }
}
```

Sugerencia didáctica: añade botones en pantallas para empujar nuevas rutas al `backStack` y practicar navegación.

---

### 6) Tema y diseño adaptativo: `Dimens` + inyección

La `inyección` de información en el contexto en Android es una técnica muy utilizada para pasar elementos a través del todo el árbol de elementos de la interfaz.

<figcaption>
  <img src="res/img/1-config/01.2-arbol-contexto.png" width="80%" align="center"  alt="">
  <p style="text-align: center; font-style: italic;">Paso de información a través del árbol de Compose</p>
</figcaption>


Queremos que la UI adapte espacios/paddings según el ancho de pantalla. Creamos un set de dimensiones y lo inyectamos por `CompositionLocal`.

```kotlin
// app/src/main/java/com/pmdm/mygamestore/presentation/ui/theme/Dimens.kt
data class Dimens(
    val extraSmall: Dp = 4.dp,
    val small: Dp = 8.dp,
    val medium: Dp = 16.dp,
    val large: Dp = 24.dp,
    val extraLarge: Dp = 32.dp,
    val paddingSmall: Dp = 8.dp,
    val paddingMedium: Dp = 16.dp,
    val paddingLarge: Dp = 24.dp,
    val cardElevation: Dp = 4.dp
)

val CompactDimens = Dimens(
    paddingSmall = 8.dp,
    paddingMedium = 16.dp,
    paddingLarge = 24.dp
)

val MediumDimens = Dimens(
    paddingSmall = 12.dp,
    paddingMedium = 24.dp,
    paddingLarge = 36.dp
)

val ExpandedDimens = Dimens(
    paddingSmall = 16.dp,
    paddingMedium = 32.dp,
    paddingLarge = 48.dp
)
```

Integración en el tema para seleccionar el set en función del ancho:

```kotlin
// app/src/main/java/com/pmdm/mygamestore/presentation/ui/theme/Theme.kt
@Composable
fun MyGameStoreTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    dynamicColor: Boolean = true,
    content: @Composable () -> Unit
) {
    val colorScheme = when {
        dynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S -> {
            val context = LocalContext.current
            if (darkTheme) dynamicDarkColorScheme(context) else dynamicLightColorScheme(context)
        }
        darkTheme -> DarkColorScheme
        else -> LightColorScheme
    }

    // Lógica para diseño adaptativo: seleccionamos dimensiones según el ancho de la pantalla
    // Siguiendo las recomendaciones de Material Design (Window Size Classes)
    val density = LocalDensity.current
    val windowInfo = LocalWindowInfo.current
    val screenWidthDp = with(density) {
        windowInfo.containerSize.width.toDp()
    }

    val dimens = when {
        screenWidthDp < 600.dp -> CompactDimens
        screenWidthDp < 840.dp -> MediumDimens
        else -> ExpandedDimens
    }

    // CompositionLocalProvider inyecta las dimensiones en el árbol de Compose
    ProvideDimensions(
        dimens = dimens,
        content = {
            MaterialTheme(
                colorScheme = colorScheme,
                typography = Typography,
                content = content
            )
        }
    )
}
```

Se crea la función `ProvideDimensions` para poder reutilizar la inyección de dimensiones en otras partes de la app.<br>
Se usa un *remember* para evitar volver a crear el set de dimensiones cada vez que se renderiza la pantalla.

```kotlin
@Composable
fun ProvideDimensions(
    dimens: Dimens,
    content: @Composable () -> Unit) {

    val dimensionSet = remember { dimens }

    // Permite proporcionar las dimensiones a lo largo del arbol de compose.
    CompositionLocalProvider(LocalDimens provides dimensionSet, content = content)
}
```

Además para facilitar el acceso a las dimensiones desde cualquier parte de la app, se crea el `LocalDimens`:

```kotlin
// app/src/main/java/com/pmdm/mygamestore/presentation/ui/theme/Dimens.kt
/**
 * CompositionLocal que permite propagar las dimensiones a través del árbol de Compose
 * sin tener que pasarlas manualmente como parámetros en cada función.
 */
private val LocalDimens = staticCompositionLocalOf { Dimens() }
```

y además se crea una propiedad de extensión para MaterialTheme que permite acceder a nuestras dimensiones
personalizadas de forma sencilla: MaterialTheme.dimens.paddingMedium

```kotlin
/**
 * Propiedad de extensión para MaterialTheme que permite acceder a nuestras dimensiones
 * personalizadas de forma sencilla: MaterialTheme.dimens.paddingMedium
 */
val MaterialTheme.dimens: Dimens
    @Composable
    @ReadOnlyComposable
    get() = LocalDimens.current
```

y uso en pantallas:

```kotlin
Text(
    text = "Home Screen",
    modifier = Modifier.padding(MaterialTheme.dimens.paddingMedium)
)
```

y por último lo único que queda es `Envolver` con un nuevo `contexto`, MaterialTheme proveyendo las Dimensiones a todo el árbol de Compose.

<figcaption>
  <img src="res/img/1-config/01.3-arbol-contexto-real.png" width="80%" align="center"  alt="">
  <p style="text-align: center; font-style: italic;">Paso de información a través del árbol de Compose</p>
</figcaption>

De tal forma, que el fichero `Theme` quedará de la siguiente forma:

```kotlin
/**
 * CompositionLocal que permite propagar las dimensiones a través del árbol de Compose
 * sin tener que pasarlas manualmente como parámetros en cada función.
 */
private val LocalDimens = staticCompositionLocalOf { Dimens() }

/**
 * Provides a custom set of dimensions to the CompositionLocal hierarchy.
 * This allows child composables to access and use specific dimension configurations
 * such as padding, spacing, or elevation, tailored for the screen size or design requirements.
 *
 * @param dimens A `Dimens` instance defining the dimension values to provide.
 * @param content A composable lambda that will have access to the provided dimensions.
 */
@Composable
fun ProvideDimensions(
    dimens: Dimens,
    content: @Composable () -> Unit) {

    val dimensionSet = remember { dimens }

    // Permite proporcionar las dimensiones a lo largo del arbol de compose.
    CompositionLocalProvider(LocalDimens provides dimensionSet, content = content)
}

/**
 * Función Composable que proporciona el tema principal de la aplicación MyGameStore.
 * Gestiona el esquema de colores (incluyendo colores dinámicos en Android 12+) 
 * y las dimensiones adaptativas según el tamaño de la pantalla.
 *
 * @param darkTheme Indica si se debe usar el tema oscuro. Por defecto usa la configuración del sistema.
 * @param dynamicColor Indica si se deben usar colores dinámicos (disponible en Android 12+).
 * @param content El contenido Composable que será envuelto por este tema.
 */
@Composable
fun MyGameStoreTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    // Los colores dinámicos están disponibles a partir de Android 12 (S)
    dynamicColor: Boolean = true,
    content: @Composable () -> Unit
) {
    // Selección del esquema de colores
    val colorScheme = when {
        dynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S -> {
            val context = LocalContext.current
            if (darkTheme) dynamicDarkColorScheme(context) else dynamicLightColorScheme(context)
        }

        darkTheme -> DarkColorScheme
        else -> LightColorScheme
    }

    // Lógica para diseño adaptativo: seleccionamos dimensiones según el ancho de la pantalla
    // Siguiendo las recomendaciones de Material Design (Window Size Classes)
    val density = LocalDensity.current
    val windowInfo = LocalWindowInfo.current
    val screenWidthDp = with(density) {
        windowInfo.containerSize.width.toDp()
    }

    val dimens = when {
        screenWidthDp < 600.dp -> CompactDimens
        screenWidthDp < 840.dp -> MediumDimens
        else -> ExpandedDimens
    }

    // CompositionLocalProvider inyecta las dimensiones en el árbol de Compose
    ProvideDimensions(
        dimens = dimens,
        content = {
            MaterialTheme(
                colorScheme = colorScheme,
                typography = Typography,
                content = content
            )
        }
    )
}

/**
 * Propiedad de extensión para MaterialTheme que permite acceder a nuestras dimensiones
 * personalizadas de forma sencilla: MaterialTheme.dimens.paddingMedium
 */
val MaterialTheme.dimens: Dimens
    @Composable
    @ReadOnlyComposable
    get() = LocalDimens.current
```


---

### 7) Paleta de colores de la app

Sustituimos los colores de plantilla por una paleta propia. Define los colores en `Color.kt` y, opcionalmente, en `colors.xml` si necesitas recursos XML.

Se deben definir los colores de nuestra App tanto en modo claro como modo oscuro. Estos colores los definiremos según el rol que tendrán en la App.

En `Color.kt`:

```kotlin
// Brand — MyGameStore. Colores compartidos para modo Light y modo Dark
val GsPurple = Color(0xFF7C2CF2)          // Primary
val GsPurpleDark = Color(0xFF6D28D9)      // Tertiary / énfasis
val GsPurpleLight = Color(0xFFA78BFA)     // Secondary

// **** MODO LIGHT ****

// Containers & variants
val GsPurpleContainer = Color(0xFFE7D7FF) // PrimaryContainer
val GsPurpleContainerOn = Color(0xFF2E1065) // onPrimaryContainer
val GsSecondaryContainer = Color(0xFFF1EAFE) // SecondaryContainer
val GsOnSecondaryContainer = Color(0xFF2E1065)

// Superficies
val GsBackground = Color(0xFFF7F3FA)
val GsSurface = Color(0xFFF7F3FA)
val GsSurfaceVariant = Color(0xFFE9DEF6)

// Texto
val GsOnPrimary = Color(0xFFFFFFFF)
val GsOnBackground = Color(0xFF1B1B1F)
val GsOnSurface = Color(0xFF1B1B1F)
val GsOnSurfaceVariant = Color(0xFF4A4458)

// Borde
val GsOutline = Color(0xFFD9C7F0)


// **** MODO DARK ****

// Superficies (dark)
val GsDarkBackground = Color(0xFF15101B)
val GsDarkSurface = Color(0xFF15101B)
val GsDarkSurfaceVariant = Color(0xFF2A2133)

// Texto (dark)
val GsDarkOnBackground = Color(0xFFEAE6F0)
val GsDarkOnSurface = Color(0xFFEAE6F0)
val GsDarkOnSurfaceVariant = Color(0xFFC9BEDA)

// Containers (opcionales usados por campos/tonos sutiles en dark)
val GsDarkPrimaryContainer = Color(0xFF3A2459)
val GsDarkOnPrimaryContainer = Color(0xFFECDCFF)
val GsDarkSecondary = Color(0xFFC4B1FF)
val GsDarkSecondaryContainer = Color(0xFF35274F)
val GsDarkOnSecondaryContainer = Color(0xFFECDCFF)

// Borde (dark)
val GsDarkOutline = Color(0xFF4A3A55)
```
> Se definen los colores para el modo Dark y modo Ligth. El prefijo `Gs` es un acrónimo de GameStore.

Mapea en `Theme.kt` los esquemas Light/Dark:

```kotlin
private val DarkColorScheme = darkColorScheme(
    primary = GsPurple,
    onPrimary = GsOnPrimary,
    primaryContainer = GsDarkPrimaryContainer,
    onPrimaryContainer = GsDarkOnPrimaryContainer,

    background = GsDarkBackground,
    onBackground = GsDarkOnBackground,

    surface = GsDarkSurface,
    onSurface = GsDarkOnSurface,
    surfaceVariant = GsDarkSurfaceVariant,
    onSurfaceVariant = GsDarkOnSurfaceVariant,
    outline = GsDarkOutline,

    secondary = GsDarkSecondary,
    onSecondary = Color(0xFF1B1329),
    secondaryContainer = GsDarkSecondaryContainer,
    onSecondaryContainer = GsDarkOnSecondaryContainer,

    tertiary = GsPurpleDark,
    onTertiary = Color(0xFFFFFFFF)
)

private val LightColorScheme = lightColorScheme(
    primary = GsPurple,
    onPrimary = GsOnPrimary,
    primaryContainer = GsPurpleContainer,
    onPrimaryContainer = GsPurpleContainerOn,

    background = GsBackground,
    onBackground = GsOnBackground,

    surface = GsSurface,
    onSurface = GsOnSurface,
    surfaceVariant = GsSurfaceVariant,
    onSurfaceVariant = GsOnSurfaceVariant,
    outline = GsOutline,

    secondary = GsPurpleLight,
    onSecondary = Color(0xFF1F1147),
    secondaryContainer = GsSecondaryContainer,
    onSecondaryContainer = GsOnSecondaryContainer,

    tertiary = GsPurpleDark,
    onTertiary = Color(0xFFFFFFFF)
)
```

Si usas recursos XML, añade en `app/src/main/res/values/colors.xml`:

```xml
<resources>
    <!-- MyGameStore — Light scheme -->
    <color name="gs_purple">#FF7C2CF2</color>
    <color name="gs_purple_dark">#FF6D28D9</color>
    <color name="gs_purple_light">#FFA78BFA</color>

    <color name="gs_on_primary">#FFFFFFFF</color>

    <color name="gs_purple_container">#FFE7D7FF</color>
    <color name="gs_purple_container_on">#FF2E1065</color>
    <color name="gs_secondary_container">#FFF1EAFE</color>
    <color name="gs_on_secondary_container">#FF2E1065</color>

    <color name="gs_background">#FFF7F3FA</color>
    <color name="gs_surface">#FFF7F3FA</color>
    <color name="gs_surface_variant">#FFE9DEF6</color>
    <color name="gs_on_background">#FF1B1B1F</color>
    <color name="gs_on_surface">#FF1B1B1F</color>
    <color name="gs_on_surface_variant">#FF4A4458</color>
    <color name="gs_outline">#FFD9C7F0</color>

    <!-- GameVault — Dark scheme -->
    <color name="gs_dark_background">#FF15101B</color>
    <color name="gs_dark_surface">#FF15101B</color>
    <color name="gs_dark_surface_variant">#FF2A2133</color>
    <color name="gs_dark_on_background">#FFEAE6F0</color>
    <color name="gs_dark_on_surface">#FFEAE6F0</color>
    <color name="gs_dark_on_surface_variant">#FFC9BEDA</color>

    <color name="gs_dark_primary_container">#FF3A2459</color>
    <color name="gs_dark_on_primary_container">#FFECDCFF</color>
    <color name="gs_dark_secondary">#FFC4B1FF</color>
    <color name="gs_dark_secondary_container">#FF35274F</color>
    <color name="gs_dark_on_secondary_container">#FFECDCFF</color>
    <color name="gs_dark_outline">#FF4A3A55</color>

  </resources>
```

---

### Pantalla de Splash

La pantalla de Splash es la primera que se muestra al iniciar la app. Simplemente muestra un logo y espera unos segundos antes de navegar a la pantalla de inicio. Para ello se utiliza `delay` dentro de un `LaunchedEffect`. El LaunchEffect se ejecuta cada vez que cambia el parámetro pasado como parámetro. En este caso `Unit` indicando que solo se va a ejecutar una vez.

Para poner el logo con vuestra imagen, cambia la imagen en `R.drawable.ic_launcher_foreground`.

```kotlin
@Composable
fun SplashScreen() {

    // Obtenemos el acceso a la navegación
    val navStack = LocalNavStack.current

    // Efecto para navegar automáticamente después de un delay
    LaunchedEffect(Unit) {
        delay(2000) // Espera 2 segundos
        navStack.add(AppRoutes.Home)  // Navega a Home
    }

    // UI del Splash Screen
    Box(
        modifier = Modifier.fillMaxSize(),
        contentAlignment = Alignment.Center
    ) {
        Image(
            painter = painterResource(id = R.drawable.ic_launcher_foreground), // Cambia esto por tu imagen
            contentDescription = "Logo de la aplicación",
            modifier = Modifier.size(200.dp)
        )
    }
}
```

Para navegar a otra pantalla, llamamos a 

### 8) Esqueleto básico de pantallas (Scaffold + paddings)

Cada pantalla puede empezar con un `Scaffold` simple y usar `MaterialTheme.dimens` para espaciados.

```kotlin
@Composable
fun HomeScreen(onNavigateToDetail: (Int) -> Unit = {}) {
    Scaffold { innerPadding ->
        Column(
            modifier = Modifier
                .padding(innerPadding)
                .padding(MaterialTheme.dimens.paddingMedium)
        ) {
            Text(text = "Home Screen", style = MaterialTheme.typography.titleLarge)
            Spacer(Modifier.height(MaterialTheme.dimens.small))
            Button(onClick = { onNavigateToDetail(42) }) {
                Text("Ir a detalle")
            }
        }
    }
}
```


### 9) Pantallas de prototipado rápido (Home, Library y Profile con BottomBar)

Para comprobar la lógica de navegación, es útil tener versiones mínimas pero funcionales de algunas pantallas. Ya que en V1 el foco es la navegación y el diseño adaptativo, vamos a usar `Scaffold`, `NavigationBar` (BottomBar) y `MaterialTheme.dimens`.

Objetivo didáctico: practicar el patrón “pantallas principales con BottomBar” y cómo se empujan rutas con `LocalNavStack`.

---

Modelo simple para items de BottomBar:

```kotlin
// Puede ir al final de HomeScreen.kt o como snippet independiente en la guía
data class BottomNavItem(
    val label: String,
    val icon: ImageVector,
    val route: AppRoutes
)
```

HomeScreen (con BottomBar):

```kotlin
@Composable
fun HomeScreen() {
    val navStack = LocalNavStack.current
    var selectedItem by remember { mutableIntStateOf(0) }

    val items = listOf(
        BottomNavItem("Home", Icons.Filled.Home, AppRoutes.Home),
        BottomNavItem("Library", Icons.Filled.Favorite, AppRoutes.Library),
        BottomNavItem("Profile", Icons.Filled.Person, AppRoutes.Profile)
    )

    Scaffold(
        bottomBar = {
            NavigationBar {
                items.forEachIndexed { index, item ->
                    NavigationBarItem(
                        icon = { Icon(item.icon, contentDescription = item.label) },
                        label = { Text(item.label) },
                        selected = selectedItem == index,
                        onClick = {
                            selectedItem = index
                            when (item.route) {
                                AppRoutes.Home -> { /* ya estamos en Home */ }
                                AppRoutes.Library -> navStack.add(AppRoutes.Library)
                                AppRoutes.Profile -> navStack.add(AppRoutes.Profile)
                                else -> {}
                            }
                        }
                    )
                }
            }
        }
    ) { innerPadding ->
        Box(
            modifier = Modifier
                .padding(innerPadding)
                .padding(MaterialTheme.dimens.paddingMedium),
            contentAlignment = Alignment.Center
        ) {
            Text("Home Screen - Catálogo de Juegos", style = MaterialTheme.typography.headlineMedium)
        }
    }
}
```

LibraryScreen (marca el segundo item como seleccionado y navega a Home/Profile):

```kotlin
@Composable
fun LibraryScreen() {
    val navStack = LocalNavStack.current
    var selectedItem by remember { mutableIntStateOf(1) }

    val items = listOf(
        BottomNavItem("Home", Icons.Filled.Home, AppRoutes.Home),
        BottomNavItem("Library", Icons.Filled.Favorite, AppRoutes.Library),
        BottomNavItem("Profile", Icons.Filled.Person, AppRoutes.Profile)
    )

    Scaffold(
        bottomBar = {
            NavigationBar {
                items.forEachIndexed { index, item ->
                    NavigationBarItem(
                        icon = { Icon(item.icon, contentDescription = item.label) },
                        label = { Text(item.label) },
                        selected = selectedItem == index,
                        onClick = {
                            selectedItem = index
                            when (item.route) {
                                AppRoutes.Home -> navStack.add(AppRoutes.Home)
                                AppRoutes.Library -> { /* ya estamos en Library */ }
                                AppRoutes.Profile -> navStack.add(AppRoutes.Profile)
                                else -> {}
                            }
                        }
                    )
                }
            }
        }
    ) { innerPadding ->
        Box(
            modifier = Modifier
                .padding(innerPadding)
                .padding(MaterialTheme.dimens.paddingMedium),
            contentAlignment = Alignment.Center
        ) {
            Text("Library Screen - Mi Biblioteca", style = MaterialTheme.typography.headlineMedium)
        }
    }
}
```

ProfileScreen (marca el tercer item como seleccionado y navega a Home/Library):

```kotlin
@Composable
fun ProfileScreen() {
    val navStack = LocalNavStack.current
    var selectedItem by remember { mutableIntStateOf(2) }

    val items = listOf(
        BottomNavItem("Home", Icons.Filled.Home, AppRoutes.Home),
        BottomNavItem("Library", Icons.Filled.Favorite, AppRoutes.Library),
        BottomNavItem("Profile", Icons.Filled.Person, AppRoutes.Profile)
    )

    Scaffold(
        bottomBar = {
            NavigationBar {
                items.forEachIndexed { index, item ->
                    NavigationBarItem(
                        icon = { Icon(item.icon, contentDescription = item.label) },
                        label = { Text(item.label) },
                        selected = selectedItem == index,
                        onClick = {
                            selectedItem = index
                            when (item.route) {
                                AppRoutes.Home -> navStack.add(AppRoutes.Home)
                                AppRoutes.Library -> navStack.add(AppRoutes.Library)
                                AppRoutes.Profile -> { /* ya estamos en Profile */ }
                                else -> {}
                            }
                        }
                    )
                }
            }
        }
    ) { innerPadding ->
        Box(
            modifier = Modifier
                .padding(innerPadding)
                .padding(MaterialTheme.dimens.paddingMedium),
            contentAlignment = Alignment.Center
        ) {
            Text("Profile Screen - Mi Perfil", style = MaterialTheme.typography.headlineMedium)
        }
    }
}
```

