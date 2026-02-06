# **6. Implementación de la Pantalla de Detalle (DetailScreen)**

En esta 6ª parte del proyecto, vamos a desarrollar la pantalla de detalle para visualizar la información completa de un videojuego. Esta fase es fundamental para aprender a manejar la **navegación con parámetros**, la **gestión de estados complejos** (Loading, Success, Error) y la creación de **componentes visuales avanzados** como carruseles y textos dinámicos.

---

## 📖 Índice

1. [Introducción](#introducción)
2. [Fase 1: Capa de Datos y Dominio (Ampliación)](#fase-1-capa-de-datos-y-dominio-ampliación)
3. [Fase 2: Capa de Presentación - ViewModel y Estado](#fase-2-capa-de-presentación-viewmodel-y-estado)
4. [Fase 3: Componentes UI de Detalle](#fase-3-componentes-ui-de-detalle)
5. [Fase 4: Pantalla DetailScreen y Navegación](#fase-4-pantalla-detailscreen-y-navegación)

---

## 🎯 Introducción {#introducción}

Hasta ahora, nuestra aplicación permite listar juegos y filtrarlos. Sin embargo, un catálogo no está completo sin una vista detallada donde el usuario pueda profundizar en la información de cada título: descripción, capturas de pantalla, desarrolladores, plataformas y más.

### ¿Qué vamos a aprender?

- ✅ **Navegación con Argumentos**: Cómo pasar el ID de un juego de una pantalla a otra.
- ✅ **ViewModel con Parámetros**: Uso de `ViewModelFactory` para inyectar datos dinámicos.
- ✅ **Ciclo de Vida y Claves**: Evitar el problema de ver datos "viejos" al navegar entre juegos.
- ✅ **Componentes Avanzados**:
    - **HorizontalPager**: Para crear carruseles de imágenes interactivos.
    - **FlowRow**: Para organizar "Chips" de forma adaptativa.
    - **ExpandableText**: Gestión de layouts para mostrar/ocultar contenido largo.

---

## 🏗️ Fase 1: Capa de Datos y Dominio (Ampliación) {#fase-1-capa-de-datos-y-dominio-ampliación}

Para que la pantalla de detalle sea visualmente atractiva, necesitamos más información de la que usamos en la `HomeScreen`.

### 1.1. Ampliación del Modelo Game

Debemos asegurarnos de que nuestra clase `Game` contenga todos los campos necesarios para el detalle.

<u>Ubicación</u>: `domain/model/Game.kt`

```kotlin
@Serializable
data class Game(
    val id: Int,
    val title: String,
    val description: String,
    val imageUrl: String,
    // ... campos ya existentes (price, rating, releaseDate, etc.) ...
    
    // Nuevos campos para el detalle
    val screenshots: List<Screenshot> = emptyList(),
    val metacritic: Int? = null,
    val developers: List<Publisher> = emptyList(),
    val publishers: List<Publisher> = emptyList(),
    val movies: List<String> = emptyList() // Para el botón de "Watch Trailer"
)
```

### 1.2. Actualización del Repositorio y Casos de Uso

El repositorio debe ser capaz de buscar un juego específico por su identificador único.

**Interfaz del Repositorio (`GamesRepository.kt`)**:
```kotlin
suspend fun getGameById(id: Int): Resource<Game>
```

**Caso de Uso (`GameUseCases.kt`)**:
Encapsulamos la lógica para que el ViewModel no hable directamente con el repositorio.

```kotlin
suspend fun getGameById(id: Int): Resource<Game> {
    return gamesRepository.getGameById(id)
}
```

---

## 🏗️ Fase 2: Capa de Presentación - ViewModel y Estado {#fase-2-capa-de-presentación-viewmodel-y-estado}

El `DetailViewModel` presenta un reto nuevo: **necesita el `gameId` nada más ser creado** para cargar los datos correctos.

### 2.1. Definición del Estado (DetailUiState)

Necesitamos representar tres cosas: el recurso del juego (que puede estar cargando), si es favorito y posibles errores.

<u>Ubicación</u>: `presentation/viewmodel/DetailViewModel.kt`

```kotlin
data class DetailUiState(
    val gameResource: Resource<Game> = Resource.Loading,
    val isFavorite: Boolean = false
)
```

### 2.2. El ViewModel con Inyección de Parámetros

El `DetailViewModel` presenta un reto nuevo: **necesita el `gameId` nada más ser creado** para saber qué juego debe cargar.

<u>¿Por qué es esto un problema para Android?</u>
Por defecto, el sistema de Android (específicamente la clase `ViewModelProvider`) espera que los ViewModels tengan un **constructor vacío** (sin parámetros). Si intentamos crear un ViewModel que recibe datos en su constructor (como `gameId` o `useCases`), Android no sabrá de dónde sacar esos valores y la aplicación fallará.

Para solucionar esto, usaremos una **Factoría (Factory)** en el siguiente paso, que actuará como un "manual de instrucciones" para que Android sepa cómo construir nuestro ViewModel correctamente.

```kotlin
class DetailViewModel(
    private val useCases: GameUseCases,
    private val gameId: Int
) : ViewModel() {

    private val _uiState = MutableStateFlow(DetailUiState())
    val uiState = _uiState.asStateFlow()

    init {
        loadGame() // Cargamos el juego al iniciar
    }

    fun loadGame() {
        viewModelScope.launch {
            _uiState.update { it.copy(gameResource = Resource.Loading) }
            val result = useCases.getGameById(gameId)
            _uiState.update { it.copy(gameResource = result) }
        }
    }
}
```

### 2.3. La Factoría (ViewModelFactory)

Para que Compose sepa cómo instanciar nuestro ViewModel con el `gameId`, creamos una clase que implemente `ViewModelProvider.Factory`.

```kotlin
class DetailViewModelFactory(
    private val useCases: GameUseCases,
    private val gameId: Int
) : ViewModelProvider.Factory {
    override fun <T : ViewModel> create(modelClass: Class<T>): T {
        return DetailViewModel(useCases, gameId) as T
    }
}
```

---

## 🏗️ Fase 3: Componentes UI de Detalle {#fase-3-componentes-ui-de-detalle}

Dada la riqueza visual de esta pantalla y su potencial reutilización, vamos a extraer los componentes complejos a la carpeta `presentation/ui/componentes`. Esto mantendrá nuestra pantalla principal limpia y modular.

### 3.1. Carrusel de Imágenes (`ImageCarousel.kt`)

Queremos que el usuario pueda deslizar entre la portada y las capturas de pantalla. Este componente se usa tanto en `DetailScreen` como en `GameCard`.

```kotlin
@Composable
fun ImageCarousel(
    images: List<String>,
    modifier: Modifier = Modifier,
    height: Dp = 300.dp,
    showIndicators: Boolean = true
) {
    val pagerState = rememberPagerState(pageCount = { images.size })
    
    Box(modifier = modifier.fillMaxWidth().height(height)) {
        HorizontalPager(state = pagerState) { page ->
            AsyncImage(
                model = images[page],
                contentScale = ContentScale.Crop,
                modifier = Modifier.fillMaxSize()
            )
        }
        
        if (showIndicators && images.size > 1) {
            PageIndicator(pagerState, images.size, Modifier.align(Alignment.BottomCenter))
        }
    }
}
```

### 3.2. Texto Expandible (`ExpandableText.kt`)

La descripción de un juego puede ser muy larga. Queremos mostrar solo 4 líneas y un botón de "Show more".

```kotlin
@Composable
fun ExpandableText(text: String, maxLines: Int = 4) {
    var expanded by remember { mutableStateOf(false) }
    var isClickable by remember { mutableStateOf(false) }

    Column(modifier = Modifier.animateContentSize()) {
        Text(
            text = text,
            maxLines = if (expanded) Int.MAX_VALUE else maxLines,
            onTextLayout = { result ->
                if (result.hasVisualOverflow || result.lineCount > maxLines) isClickable = true
            }
        )
        if (isClickable) {
            Text(
                text = if (expanded) "Show less" else "Show more",
                color = MaterialTheme.colorScheme.primary,
                modifier = Modifier.clickable { expanded = !expanded }
            )
        }
    }
}
```

### 3.3. Componentes de Información (`DetailComponents.kt`)

Agrupamos componentes pequeños como `DetailChip`, `InfoColumn` y `MetacriticBadge` para organizar los metadatos del juego.

```kotlin
@Composable
fun InfoColumn(label: String, value: String = "", content: @Composable (() -> Unit)? = null) {
    Column(modifier = Modifier.width(180.dp)) {
        Text(text = label, style = MaterialTheme.typography.labelLarge)
        if (content != null) content() else Text(text = value)
    }
}

@Composable
fun MetacriticBadge(score: Int) {
    Surface(color = Color(0xFF00CE7A), shape = RoundedCornerShape(4.dp)) {
        Text(text = score.toString(), color = Color.White)
    }
}
```

### 3.4. Layouts de Chips (`ChipsLayouts.kt`)

Para organizar múltiples etiquetas, usamos `TagsFlowRow` (varias líneas) o `ChipsPager` (una línea con scroll).

```kotlin
@Composable
fun TagsFlowRow(items: List<String>) {
    FlowRow(
        horizontalArrangement = Arrangement.spacedBy(MaterialTheme.dimens.small),
        maxLines = 2
    ) {
        items.forEach { DetailChip(it) }
    }
}
```

---

## 🏗️ Fase 4: Pantalla DetailScreen y Navegación {#fase-4-pantalla-detailscreen-y-navegación}

### 4.1. Configuración de la Ruta

En nuestro sistema de navegación, la ruta de detalle ahora requiere un parámetro.

<u>Ubicación</u>: `presentation/ui/navigation/AppRoutes.kt`
```kotlin
@Serializable
data class Detail(val gameId: Int) : AppRoutes
```

Y en el `NavGraph.kt`:
```kotlin
entry<AppRoutes.Detail> { route ->
    DetailScreen(
        gameId = route.gameId,
        onBack = { navStack.removeLastOrNull() }
    )
}
```

### 4.2. El problema de la "Clave" (Crucial)

!!! failure "Error común"
    Si navegas del Juego A al Juego B, Compose podría reutilizar el mismo ViewModel del Juego A, haciendo que veas los datos incorrectos.

**Solución**: Pasar una `key` única al obtener el ViewModel.

```kotlin
@Composable
fun DetailScreen(gameId: Int, onBack: () -> Unit) {
    val viewModel: DetailViewModel = viewModel(
        // 🔑 Esta clave fuerza a crear un nuevo VM si el ID cambia
        key = "DetailViewModel_$gameId", 
        factory = DetailViewModelFactory(GameUseCases(repository), gameId)
    )
    
    val uiState by viewModel.uiState.collectAsState()
    
    // ... Resto del Scaffold con LazyColumn para el contenido ...
}
```

### 4.3. Estructura del Contenido (`LazyColumn`)

Usamos un `LazyColumn` para que toda la pantalla sea desplazable, y configuramos la imagen para que sea "a sangre" (sin padding lateral), mientras que el texto sí lo tiene.

```kotlin
LazyColumn {
    item { ImageCarousel(images) }
    item {
        Column(modifier = Modifier.padding(MaterialTheme.dimens.medium)) {
            Text(
                text = game.title, 
                style = MaterialTheme.typography.headlineMedium.copy(
                    fontWeight = FontWeight.Bold,
                    fontSize = 28.sp
                )
            )
            // ... Rating, Developers, Genres ...
            ExpandableText(game.description)
        }
    }
}
```

### 4.4. UI Limpia y Profesional

Para lograr un acabado profesional, hemos realizado dos ajustes finales:

1. **TopAppBar Transparente**: Al navegar al detalle, la barra superior es transparente y no tiene título, permitiendo que la imagen del juego luzca en todo su esplendor.
2. **Botón de Favoritos**: Un botón flotante junto al título con estados visuales claros (corazón relleno/vacío).

```kotlin
IconButton(
    onClick = onToggleFavorite,
    modifier = Modifier
        .size(MaterialTheme.dimens.buttonHeightLarge)
        .clip(RoundedCornerShape(MaterialTheme.dimens.medium))
) {
    Icon(
        imageVector = if (isFavorite) Icons.Default.Favorite else Icons.Default.FavoriteBorder,
        contentDescription = "Favorite",
        tint = if (isFavorite) Color.Red else MaterialTheme.colorScheme.onSurfaceVariant
    )
}
```

---

## ✅ Resumen de la Fase 6

¡Felicidades! Has implementado una de las pantallas más complejas de la aplicación. En esta fase has dominado:

1.  **Arquitectura de Datos**: Cómo pedir un recurso específico por su ID.
2.  **Compose Avanzado**: Gestión de estados de texto dinámicos y carruseles.
3.  **Navegación Robusta**: Uso de argumentos y gestión del ciclo de vida de los ViewModels mediante claves.
4.  **UI Minimalista**: Creación de una experiencia inmersiva mediante el uso de transparencias y la eliminación de elementos redundantes como títulos de cabecera.
5.  **Diseño Adaptativo con Dimens**: Uso de `MaterialTheme.dimens` para mantener la consistencia visual en diferentes tamaños de pantalla.
6.  **UX Pulida**: Feedback de carga al usuario y estados de error controlados.

Puedes revisar la implementación completa en `presentation/ui/screens/DetailScreen.kt`.
