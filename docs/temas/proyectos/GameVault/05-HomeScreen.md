# **5. Implementación de la Paantalla de HomeScreen con Catálogo de Juegos**

En esta 5ª parte del proyecto, vamos a implementar la pantalla de inicio de la aplicación, que mostrará un catálogo de juegos con funcionalidades de búsqueda, filtrado y navegación.

!!! tip "Repositorio de la Aplicación"
El código fuente de la aplicación se encuentra en el repositorio de GitHub: [MyGameStore](https://github.com/jssdocente/MyGameStore)

## 📖 Índice

1. [Introducción](#introducción)
2. [Arquitectura General](#arquitectura-general)
3. [Fase 1: Capa de Datos - Models y Repository](#fase-1-capa-de-datos)
4. [Fase 2: Capa de Dominio - UseCases](#fase-2-capa-de-dominio)
5. [Fase 3: Capa de Presentación - ViewModel](#fase-3-capa-de-presentación)
6. [Fase 4: Componentes UI Reutilizables](#fase-4-componentes-ui)
7. [Fase 5: HomeScreen Completa](#fase-5-homescreen)

---

## 🎯 Introducción {#introducción}

En esta guía implementaremos la **pantalla principal (HomeScreen)** de MyGameStore, transformándola de una pantalla vacía en un catálogo completo y funcional de juegos.

### ¿Qué vamos a construir?

Una pantalla con las siguientes capacidades:

- ✅ **Búsqueda en tiempo real**: Los usuarios podrán buscar juegos por título o descripción
- ✅ **Filtrado por categorías**: ACTION, RPG, ADVENTURE, STRATEGY, SPORTS, etc.
- ✅ **Filtrado por plataformas**: PC, PlayStation, Xbox, Nintendo, Mobile
- ✅ **Filtrado por fecha**: Últimos 7 días, 30 días, 90 días
- ✅ **Filtrado por géneros**: Múltiples géneros por juego
- ✅ **Navegación**: Click en un juego para ver detalles
- ✅ **Barra de navegación inferior**: Acceso rápido a Home, Library, Profile
- ✅ **Gestión de estados**: Loading, Success, Empty, Error

### Arquitectura que seguiremos

Esta implementación sigue **Clean Architecture** con separación clara en tres capas:

```
┌─────────────────────────────────────┐
│     PRESENTATION LAYER (UI)         │
│  - HomeScreen (Composables)         │
│  - HomeViewModel (Estado)           │
└──────────────┬──────────────────────┘
               │
┌──────────────▼──────────────────────┐
│       DOMAIN LAYER (Lógica)         │
│  - GameUseCases (Casos de uso)      │
│  - Game, Platform, Category (Models)│
└──────────────┬──────────────────────┘
               │
┌──────────────▼──────────────────────┐
│       DATA LAYER (Datos)            │
│  - GamesRepository (Interfaz)       │
│  - GamesRepositoryImpl (Mock)       │
└─────────────────────────────────────┘
```


### ¿Por qué Clean Architecture?

- **Separación de responsabilidades**: Cada capa tiene un propósito claro
- **Testeable**: Fácil hacer testing en cada capa
- **Mantenible**: Cambios en una capa no afectan a otras
- **Escalable**: Preparado para crecer (API real, más funcionalidades)
- **Independiente del framework**: La lógica de negocio no depende de Android

---

## 🏗️ Arquitectura General {#arquitectura-general}

### Diagrama de Capas

```
┌────────────────────────────────────────────────────┐
│            PRESENTATION LAYER                      │
│                                                    │
│  ┌──────────────────┐      ┌──────────────────┐    │
│  │   HomeScreen     │◄─────│  HomeViewModel   │    │
│  │  (Composable)    │      │   (StateFlow)    │    │
│  │                  │      │                  │    │
│  │ - SearchBar      │      │ - HomeUiState    │    │
│  │ - CategoryRow    │      │ - loadGames()    │    │
│  │ - GameGrid       │      │ - onSearch()     │    │
│  │ - BottomNavBar   │      │ - onFilter()     │    │
│  └──────────────────┘      └────────┬─────────┘    │
│                                     │              │
└─────────────────────────────────────┼──────────────┘
                                      │
┌─────────────────────────────────────┼──────────────┐
│            DOMAIN LAYER             │              │
│                                     ▼              │
│                          ┌─────────────────────┐   │
│                          │   GameUseCases      │   │
│                          │                     │   │
│                          │ - getAllGames()     │   │
│                          │ - searchGames()     │   │
│                          │ - getByCategory()   │   │
│                          │ - getByPlatform()   │   │
│                          │ - getByInterval()   │   │
│                          │ - getByGenres()     │   │
│                          └──────────┬──────────┘   │
│                                     │              │
└─────────────────────────────────────┼──────────────┘
                                      │
┌─────────────────────────────────────┼──────────────┐
│            DATA LAYER               │              │
│                                     ▼              │
│                          ┌─────────────────────┐   │
│                          │  GamesRepository    │   │
│                          │    (Interface)      │   │
│                          └──────────┬──────────┘   │
│                                     │              │
│                          ┌──────────▼──────────┐   │
│                          │ GamesRepositoryImpl │   │
│                          │   (Mock Data)       │   │
│                          │                     │   │
│                          │ - mockGames: List   │   │
│                          │ - getAllGames()     │   │
│                          │ - searchGames()     │   │
│                          └─────────────────────┘   │
│                                                    │
└────────────────────────────────────────────────────┘
```


### Flujo de Datos

*1. Usuario interactúa con la UI* (escribe en búsqueda, click en filtro)
```
HomeScreen → viewModel.onSearchQueryChange("witcher")
```


*2. ViewModel procesa el evento* y llama al caso de uso
```
HomeViewModel → gameUseCases.searchGames("witcher")
```


*3. UseCase ejecuta lógica de negocio* y llama al repository
```
GameUseCases → gamesRepository.searchGames("witcher")
```


*4. Repository obtiene los datos* (mock, API, DB)
```
GamesRepositoryImpl → Flow<List<Game>>
```


*5. Datos fluyen de vuelta* hacia la UI
```
Repository → UseCase → ViewModel → UI State → HomeScreen (recomposición)
```

---

##  FASE 1: Capa de Datos - Models y Repository {#fase-1-capa-de-datos}

La capa de datos es responsable de **obtener y gestionar los datos** de la aplicación. En esta fase crearemos:

1. Modelos de dominio (Game, GameCategory, Platform, DateInterval)
2. Resource y AppError para manejo de estados
3. MockGamesDataSource con datos de prueba
4. Interfaz del Repository
5. Implementación mock del Repository

---

###  Paso 1.1: Crear el modelo Game

El modelo `Game` representa un videojuego en nuestro catálogo. Es una **entidad de dominio**, lo que significa que pertenece a la lógica de negocio y es independiente de frameworks.

**Ubicación**: `app/src/main/java/com/pmdm/mygamestore/domain/model/Game.kt`

```kotlin
package com.pmdm.mygamestore.domain.model

import kotlinx.serialization.Serializable

/**
 *  Modelo de dominio que representa un juego en el catálogo
 *
 * CARACTERÍSTICAS:
 * - Inmutable (val): No se puede modificar después de creación
 * - Data class: Kotlin genera equals, hashCode, toString, copy automáticamente
 * - Serializable: Puede ser serializado para navegación con parámetros
 * - Domain model: Pertenece a la capa de dominio, no a UI ni datos
 *
 * @property id Identificador único del juego (usado como key en listas)
 * @property slug Identificador único en formato URL-friendly
 * @property title Título del juego
 * @property description Descripción detallada del juego
 * @property imageUrl URL de la imagen de portada (cargada con Coil)
 * @property price Precio en formato decimal (ej: 59.99)
 * @property rating Valoración de 0.0 a 5.0 (ej: 4.8)
 * @property releaseDate Fecha de lanzamiento en formato ISO (yyyy-MM-dd): "2024-01-15"
 * @property category Categoría principal del juego (ACTION, RPG, etc.)
 * @property platforms Lista de plataformas en las que está disponible
 * @property genres Lista de géneros del juego
 * @property stores Lista de tiendas donde está disponible
 * @property tags Lista de etiquetas/tags del juego
 * @property screenshots Lista de capturas de pantalla
 * @property metacritic Puntuación de Metacritic (0-100)
 * @property playtime Tiempo de juego promedio en horas
 * @property ratingsCount Número total de valoraciones
 * @property esrbRating Clasificación ESRB del juego
 */
@Serializable
data class Game(
    val id: Int,
    val slug: String? = null,
    val title: String,
    val description: String,
    val imageUrl: String,
    val price: Double,
    val rating: Double,
    val releaseDate: String,
    val category: GameCategory,
    val platforms: List<Platform> = emptyList(),
    val genres: List<Genre> = emptyList(),
    val stores: List<Store> = emptyList(),
    val tags: List<Tag> = emptyList(),
    val screenshots: List<Screenshot> = emptyList(),
    val metacritic: Int? = null,
    val playtime: Int? = null,
    val ratingsCount: Int? = null,
    val esrbRating: EsrbRating? = null
)
```


**Conceptos clave:**

**Data class en Kotlin:**
```kotlin
// Kotlin genera automáticamente:
// - equals(): compara por contenido
// - hashCode(): para usar en colecciones
// - toString(): representación en String
// - copy(): crea copias con modificaciones

val game1 = Game(id = 1, title = "Witcher", ...)
val game2 = game1.copy(price = 29.99) // Copia modificando solo el precio
```


**@Serializable:**
- Permite convertir el objeto a/desde JSON o otros formatos
- Necesario para navegación con parámetros tipo-safe
- Usado por la librería navigation3

**Inmutabilidad (val):**
- Los datos no cambian después de creación
- Previene bugs por modificaciones accidentales
- Ideal para arquitecturas reactivas (StateFlow, Flow)

---

###  Paso 1.2: Crear enums para categorías, plataformas e intervalos

Los **enums** nos permiten definir conjuntos cerrados de valores posibles, proporcionando type-safety y evitando errores.

**Ubicación**: `app/src/main/java/com/pmdm/mygamestore/domain/model/GameEnums.kt`

```kotlin
package com.pmdm.mygamestore.domain.model

/**
 *  Categorías principales de juegos
 *
 * Representa los géneros principales disponibles en el catálogo.
 * Usado para filtrar juegos por categoría.
 */
enum class GameCategory {
    ALL,        // Todas las categorías (sin filtro)
    ACTION,     // Juegos de acción (God of War, Halo)
    ADVENTURE,  // Aventuras (Zelda, Uncharted)
    RPG,        // Role-Playing Games (Witcher, Elden Ring)
    STRATEGY,   // Estrategia (Civilization, Age of Empires)
    SPORTS,     // Deportes (FIFA, Forza)
    SIMULATION, // Simulación (Stardew Valley, Minecraft)
    PUZZLE      // Puzzles (Tetris, Portal)
}

/**
 *  Plataformas de videojuegos
 *
 * Representa las plataformas en las que un juego está disponible.
 * Usado para filtrar juegos por plataforma.
 */
enum class Platform {
    ALL,         // Todas las plataformas (sin filtro)
    PC,          // Windows, Mac, Linux
    PLAYSTATION, // PS4, PS5
    XBOX,        // Xbox One, Xbox Series X/S
    NINTENDO,    // Nintendo Switch
    MOBILE       // iOS, Android
}

/**
 *  Intervalos de fechas para filtrar lanzamientos
 *
 * Permite filtrar juegos según cuándo fueron lanzados.
 * Útil para secciones como "Novedades de la semana" o "Lanzamientos recientes".
 */
enum class DateInterval {
    ALL_TIME,      // Todos los tiempos (sin filtro de fecha)
    LAST_WEEK,     // Últimos 7 días
    LAST_30_DAYS,  // Último mes
    LAST_90_DAYS   // Últimos 3 meses
}
```


** ¿Por qué usar enums?**

**Ventajas:**

1. **Type-safety**: El compilador previene valores inválidos
```kotlin
// ✅ Correcto
val category: GameCategory = GameCategory.RPG

// ❌ Error de compilación
val category: GameCategory = "RPG" // No compila
```


2. **Exhaustive when**: El compilador verifica que manejamos todos los casos
```kotlin
when (category) {
    GameCategory.ALL -> // ...
    GameCategory.ACTION -> // ...
    GameCategory.RPG -> // ...
    // Si falta un caso, el compilador avisa
}
```


3. **Autocomplete**: El IDE sugiere valores válidos
```kotlin
val cat = GameCategory. // IDE muestra: ALL, ACTION, RPG, etc.
```


4. **Refactoring seguro**: Renombrar un valor actualiza todo el código

5. **Iterable**: Podemos iterar sobre todos los valores
```kotlin
GameCategory.entries.forEach { category ->
    // Procesar cada categoría
}
```


---

###  Paso 1.3: Crear Resource y AppError para manejo de estados

Antes de crear el Repository, definimos cómo manejaremos estados y errores de forma robusta y type-safe.

**Ubicación**: `app/src/main/java/com/pmdm/mygamestore/domain/model/Resource.kt`

```kotlin
package com.pmdm.mygamestore.domain.model

/**
 *  Sealed class que representa el estado de una operación
 *
 * PATRÓN RESOURCE/RESULT:
 * ✅ Manejo explícito de estados (Loading, Success, Error)
 * ✅ Type-safe: El compilador obliga a manejar todos los casos
 * ✅ Errores tipados con información específica
 * ✅ Elimina null checks y excepciones no controladas
 *
 * VENTAJAS:
 * - Exhaustive when: El compilador verifica todos los casos
 * - Sin null: Evita NullPointerException
 * - Errores descriptivos: Sabemos qué falló exactamente
 * - UI reactiva: La UI puede reaccionar a cada estado
 *
 * FLUJO TÍPICO:
 * 1. Loading → Mostrar spinner
 * 2. Success → Mostrar datos
 * 3. Error → Mostrar mensaje de error
 *
 * @param T Tipo de dato que contiene en caso de éxito
 */
sealed class Resource<out T> {
    
    /**
     * ⏳ Estado: Operación en progreso
     *
     * Se emite al iniciar una operación asíncrona.
     * La UI muestra loading indicator.
     *
     * Ejemplo:
     * ```
     * when (resource) {
     *     is Resource.Loading -> showLoadingSpinner()
     * }
     * ```
     */
    data object Loading : Resource<Nothing>()
    
    /**
     * ✅ Estado: Operación completada exitosamente
     *
     * Contiene los datos solicitados.
     *
     * @param data Datos obtenidos de la operación
     *
     * Ejemplo:
     * ```
     * when (resource) {
     *     is Resource.Success -> {
     *         val games = resource.data
     *         showGames(games)
     *     }
     * }
     * ```
     */
    data class Success<T>(val data: T) : Resource<T>()
    
    /**
     * ❌ Estado: Operación falló
     *
     * Contiene información detallada del error.
     *
     * @param error Error específico que ocurrió
     *
     * Ejemplo:
     * ```
     * when (resource) {
     *     is Resource.Error -> {
     *         when (resource.error) {
     *             is AppError.NetworkError -> showNoInternetMessage()
     *             is AppError.NotFound -> showNotFoundMessage()
     *         }
     *     }
     * }
     * ```
     */
    data class Error(val error: AppError) : Resource<Nothing>()
}

/**
 *  Sealed class que representa errores específicos de la app
 *
 * Permite manejar diferentes tipos de errores de forma específica:
 * - Errores de red (sin conexión, timeout)
 * - Errores de base de datos (corrupción, falta de espacio)
 * - Errores de negocio (no encontrado, no autorizado)
 * - Errores de validación
 * - Errores desconocidos
 *
 * VENTAJAS:
 * ✅ Errores tipados y específicos
 * ✅ La UI puede mostrar mensajes personalizados
 * ✅ Fácil logging y analytics
 * ✅ Manejo exhaustivo con when
 */
sealed class AppError {
    
    /**
     *  Error de red
     *
     * Ocurre cuando:
     * - No hay conexión a Internet
     * - Timeout de la petición
     * - Error del servidor (5xx)
     *
     * @param message Descripción del error
     *
     * Ejemplo de uso:
     * ```
     * when (error) {
     *     is AppError.NetworkError -> {
     *         showSnackbar("Check your internet connection")
     *     }
     * }
     * ```
     */
    data class NetworkError(val message: String) : AppError()
    
    /**
     *  Error de base de datos
     *
     * Ocurre cuando:
     * - No se puede acceder a la base de datos
     * - Datos corruptos
     * - Falta de espacio en disco
     *
     * @param message Descripción del error
     */
    data class DatabaseError(val message: String) : AppError()
    
    /**
     *  Recurso no encontrado (404)
     *
     * Ocurre cuando:
     * - El juego con ID especificado no existe
     * - La búsqueda no tiene resultados
     * - La categoría no tiene juegos
     *
     * Ejemplo de uso:
     * ```
     * when (error) {
     *     is AppError.NotFound -> {
     *         showEmptyState("No games found")
     *     }
     * }
     * ```
     */
    data object NotFound : AppError()
    
    /**
     *  No autorizado (401/403)
     *
     * Ocurre cuando:
     * - El usuario no tiene sesión activa
     * - El token de autenticación expiró
     * - No tiene permisos para la operación
     *
     * Ejemplo de uso:
     * ```
     * when (error) {
     *     is AppError.Unauthorized -> {
     *         navigateToLogin()
     *     }
     * }
     * ```
     */
    data object Unauthorized : AppError()
    
    /**
     * ⚠️ Error de validación
     *
     * Ocurre cuando:
     * - Query de búsqueda inválido
     * - Parámetros fuera de rango
     * - Formato de datos incorrecto
     *
     * @param message Descripción del error de validación
     *
     * Ejemplo de uso:
     * ```
     * when (error) {
     *     is AppError.ValidationError -> {
     *         showError(error.message)
     *     }
     * }
     * ```
     */
    data class ValidationError(val message: String) : AppError()
    
    /**
     * ❓ Error desconocido
     *
     * Ocurre cuando:
     * - Excepción no prevista
     * - Error sin categoría específica
     *
     * @param message Descripción del error
     *
     * Ejemplo de uso:
     * ```
     * when (error) {
     *     is AppError.Unknown -> {
     *         logError(error.message)
     *         showGenericError()
     *     }
     * }
     * ```
     */
    data class Unknown(val message: String) : AppError()
}
```


**Conceptos clave de Resource:**

**1. ¿Por qué Resource<T> y no solo T?**

```kotlin
// ❌ Sin Resource: No sabemos si está cargando, si falló, etc.
fun getAllGames(): Flow<List<Game>>

// ✅ Con Resource: Estados explícitos
fun getAllGames(): Flow<Resource<List<Game>>>
```


**2. Pattern matching con when exhaustivo:**

```kotlin
when (val result = resource) {
    is Resource.Loading -> {
        // Mostrar loading
        showLoadingIndicator()
    }
    is Resource.Success -> {
        val games = result.data
        // Mostrar juegos
        displayGames(games)
    }
    is Resource.Error -> {
        when (result.error) {
            is AppError.NetworkError -> showNoInternetDialog()
            is AppError.NotFound -> showEmptyState()
            is AppError.Unknown -> showGenericError()
            // El compilador obliga a manejar todos los casos
        }
    }
}
```


**3. Type-safety en errores:**

```kotlin
// ❌ Sin tipos: ambiguo, difícil de manejar
throw Exception("Network error")

// ✅ Con tipos: claro y específico
AppError.NetworkError("No internet connection")
```


---

###  Paso 1.4: Crear MockGamesDataSource

Separamos los datos mock en su propia clase para mantener limpio el repository y facilitar la migración a API real.

**Ubicación**: `app/src/main/java/com/pmdm/mygamestore/data/local/MockDataSource.kt`

```kotlin
package com.pmdm.mygamestore.data.local

/**
 * MockDataSource es un objeto que simula una fuente de datos para una aplicación centrada en videojuegos.
 * Sirve como recurso inicial para proporcionar información estática relacionada con géneros, plataformas,
 * editores, tiendas, etiquetas y juegos.
 *
 * Datos proveídos:
 * - `genres`: Lista de géneros populares de videojuegos, definidos por un identificador único, nombre y slug.
 * - `platforms`: Lista de plataformas disponibles para videojuegos, con su respectivo identificador, nombre y slug.
 * - `publishers`: Lista de editores de videojuegos reconocidos, identificados por un ID, nombre y slug.
 * - `stores`: Lista de tiendas en las que se pueden adquirir juegos o contenido relacionado, con su correspondiente identificador, nombre y slug.
 * - `tags`: Lista de etiquetas (tags) relevantes para clasificar los videojuegos, definidas por ID, nombre y slug.
 * - `games`: Lista de detalles de juegos predefinidos, incluyendo información como título, descripción, precio, calificación, fecha de lanzamiento,
 *  plataformas soportadas, géneros relacionados, tiendas donde están disponibles, etiquetas asociadas, capturas de pantalla, entre otros atributos específicos
 * .
 */
object MockDataSource {

    /**
     *  Lista de juegos mock para desarrollo
     *
     * En producción real, esto vendría de:
     * - API REST: GET https://api.example.com/games
     * - Base de datos: Room + SQLite
     * - Firebase: Firestore collection "games"
     * - Caché híbrida: Room + Retrofit con política de caché
     */
    val games = listOf(
        // ... contenido
    )

    val platforms = listOf(
        Platform(id = 4, name = "PC", slug = "pc"),
        Platform(id = 187, name = "PlayStation 5", slug = "playstation5"),
        Platform(id = 18, name = "PlayStation 4", slug = "playstation4"),
        Platform(id = 1, name = "PlayStation 3", slug = "playstation3"),
        Platform(id = 186, name = "Xbox Series S/X", slug = "xbox-series-x"),
        Platform(id = 14, name = "Xbox One", slug = "xbox-one"),
        Platform(id = 80, name = "Xbox 360", slug = "xbox360"),
        Platform(id = 7, name = "Nintendo Switch", slug = "nintendo-switch"),
        Platform(id = 3, name = "iOS", slug = "ios"),
        Platform(id = 21, name = "Android", slug = "android")
    )

    val publishers = listOf(
        Publisher(id = 354, name = "Electronic Arts", slug = "electronic-arts"),
        Publisher(id = 3408, name = "Ubisoft Entertainment", slug = "ubisoft-entertainment"),
        Publisher(id = 339, name = "Bethesda Softworks", slug = "bethesda-softworks"),
        Publisher(id = 3399, name = "Rockstar Games", slug = "rockstar-games"),
        Publisher(id = 33, name = "Warner Bros. Interactive", slug = "warner-bros-interactive"),
        Publisher(id = 209, name = "Sony Interactive Entertainment", slug = "sony-interactive-entertainment"),
        Publisher(id = 45, name = "Microsoft Xbox Game Studios", slug = "microsoft-xbox-game-studios"),
        Publisher(id = 16257, name = "Nintendo", slug = "nintendo"),
        Publisher(id = 9191, name = "Sega", slug = "sega-2"),
        Publisher(id = 3390, name = "Square Enix", slug = "square-enix"),
        Publisher(id = 347, name = "Capcom", slug = "capcom"),
        Publisher(id = 345, name = "Activision Blizzard", slug = "activision-blizzard"),
        Publisher(id = 25, name = "2K Games", slug = "2k-games"),
        Publisher(id = 208, name = "Bandai Namco Entertainment", slug = "bandai-namco-entertainment"),
        Publisher(id = 3410, name = "Deep Silver", slug = "deep-silver")
    )

    val stores = listOf(
        Store(id = 1, name = "Steam", slug = "steam"),
        Store(id = 3, name = "PlayStation Store", slug = "playstation-store"),
        Store(id = 2, name = "Xbox Store", slug = "xbox-store"),
        Store(id = 4, name = "App Store", slug = "apple-appstore"),
        Store(id = 8, name = "Google Play", slug = "google-play"),
        Store(id = 5, name = "GOG", slug = "gog"),
        Store(id = 6, name = "Nintendo Store", slug = "nintendo"),
        Store(id = 7, name = "Xbox 360 Store", slug = "xbox360"),
        Store(id = 9, name = "itch.io", slug = "itch"),
        Store(id = 11, name = "Epic Games", slug = "epic-games")
    )

    val tags = listOf(
        Tag(id = 31, name = "Singleplayer", slug = "singleplayer"),
        Tag(id = 40836, name = "Full controller support", slug = "full-controller-support"),
        Tag(id = 7, name = "Multiplayer", slug = "multiplayer"),
        Tag(id = 40847, name = "Steam Achievements", slug = "steam-achievements"),
        Tag(id = 13, name = "Atmospheric", slug = "atmospheric"),
        Tag(id = 40849, name = "Steam Cloud", slug = "steam-cloud"),
        Tag(id = 42, name = "Great Soundtrack", slug = "great-soundtrack"),
        Tag(id = 24, name = "RPG", slug = "rpg"),
        Tag(id = 18, name = "Co-op", slug = "co-op"),
        Tag(id = 118, name = "Story Rich", slug = "story-rich"),
        Tag(id = 411, name = "Cooperative", slug = "cooperative"),
        Tag(id = 8, name = "First-Person", slug = "first-person"),
        Tag(id = 32, name = "Sci-fi", slug = "sci-fi"),
        Tag(id = 149, name = "Third Person", slug = "third-person"),
        Tag(id = 4, name = "Funny", slug = "funny"),
        Tag(id = 37, name = "Sandbox", slug = "sandbox"),
        Tag(id = 123, name = "Comedy", slug = "comedy"),
        Tag(id = 64, name = "Fantasy", slug = "fantasy"),
        Tag(id = 147, name = "2D", slug = "2d")
    )
}
```


---

###  Paso 1.5: Crear interfaz GamesRepository

El **patrón Repository** abstrae el origen de los datos. Definimos un contrato que cualquier implementación (mock o real) debe cumplir.

**Ubicación**: `app/src/main/java/com/pmdm/mygamestore/data/repository/GamesRepository.kt`

```kotlin
package com.pmdm.mygamestore.data.repository

/**
 *  Interfaz que define el contrato del repositorio de juegos
 *
 * PATRÓN REPOSITORY:
 * ✅ Abstrae la fuente de datos (API, DB, Mock)
 * ✅ Permite múltiples implementaciones
 * ✅ Facilita testing con mocks
 * ✅ Aplica principio de Inversión de Dependencias (SOLID)
 *
 * IMPLEMENTACIONES:
 * 1. MockGamesRepositoryImpl → Desarrollo local, filtra en memoria
 * 2. GamesRepositoryImpl → Producción, filtra en API/backend
 *
 * BENEFICIOS:
 * - Desacoplamiento: UseCases no saben de dónde vienen los datos
 * - Testing: Fácil crear implementaciones de prueba
 * - Flexibilidad: Cambiar de mock a API sin modificar UseCases
 * - Mantenibilidad: Un solo punto de cambio para origen de datos
 *
 * IMPORTANTE - Resource Pattern:
 * Todos los métodos devuelven Flow<Resource<T>> para manejar:
 * - Loading: Operación en progreso
 * - Success: Datos obtenidos correctamente
 * - Error: Algo falló con información específica
 */
interface GamesRepository {
    /**
    * Obtiene todos los juegos disponibles en el catálogo
    */
    fun getAllGames(): Flow<Resource<List<Game>>>

    /**
     * Busca juegos combinando múltiples criterios de filtrado
     */
    fun getFilteredGames(
        query: String = "",
        category: GameCategory = GameCategory.ALL,
        platform: PlatformEnum = PlatformEnum.ALL,
        interval: DateInterval = DateInterval.ALL_TIME
    ): Flow<Resource<List<Game>>>

    /**
     * Obtiene un juego específico por su ID
     */
    suspend fun getGameById(id: Int): Resource<Game>
}
```


---

###  Paso 1.6: Implementar MockGamesRepositoryImpl

Esta implementación **simula** lo que haría una API real, pero filtrando datos en memoria. Es para desarrollo sin depender de backend.

**Ubicación**: `app/src/main/java/com/pmdm/mygamestore/data/repository/MockGamesRepositoryImpl.kt`

```kotlin
package com.pmdm.mygamestore.data.repository

/**
 *  Implementación MOCK del repositorio de juegos
 *
 * PROPÓSITO:
 * - Desarrollo sin depender de backend/API
 * - Testing con datos controlados
 * - Simular comportamiento de API real
 *
 * CARACTERÍSTICAS:
 * ✅ Filtra datos en MEMORIA (no en servidor)
 * ✅ Simula delays de red para testing realista
 * ✅ Devuelve Resource (Loading → Success/Error)
 * ✅ Maneja errores con try-catch
 *
 * IMPORTANTE - Filtros locales:
 * Esta implementación filtra MockGamesDataSource en el DISPOSITIVO.
 * En una API real, los filtros se ejecutarían en el BACKEND.
 *
 * Ejemplo de diferencia:
 */
class MockGamesRepositoryImpl : GamesRepository {
    private val dataSource = MockDataSource

    private suspend fun simulateNetworkDelay() {
        delay(800)
    }

    override fun getAllGames(): Flow<Resource<List<Game>>> = flow {
        try {
            emit(Resource.Loading)
            simulateNetworkDelay()
            emit(Resource.Success(dataSource.games))
        } catch (e: Exception) {
            emit(Resource.Error(AppError.Unknown(e.message ?: "Unknown error")))
        }
    }

    @RequiresApi(Build.VERSION_CODES.O)
    override fun getFilteredGames(
        query: String,
        category: GameCategory,
        platform: PlatformEnum,
        interval: DateInterval
    ): Flow<Resource<List<Game>>> = flow {
        try {
            emit(Resource.Loading)
            simulateNetworkDelay()

            var filtered = dataSource.games

            // Filtro por Query
            if (query.isNotBlank()) {
                filtered = filtered.filter { game ->
                    game.title.contains(query, ignoreCase = true) ||
                            game.description.contains(query, ignoreCase = true)
                }
            }

            // Filtro por Categoría
            if (category != GameCategory.ALL) {
                filtered = filtered.filter { it.category == category }
            }

            // Filtro por Plataforma
            if (platform != PlatformEnum.ALL) {
                filtered = filtered.filter { game ->
                    game.platforms.any { p ->
                        when (platform) {
                            PlatformEnum.PC -> p.slug.contains("pc", ignoreCase = true)
                            PlatformEnum.PLAYSTATION -> p.slug.contains("playstation", ignoreCase = true)
                            PlatformEnum.XBOX -> p.slug.contains("xbox", ignoreCase = true)
                            PlatformEnum.NINTENDO -> p.slug.contains("nintendo", ignoreCase = true)
                            PlatformEnum.MOBILE -> p.slug.contains("android", ignoreCase = true) || p.slug.contains("ios", ignoreCase = true)
                            else -> false
                        }
                    }
                }
            }

            // Filtro por Intervalo
            if (interval != DateInterval.ALL_TIME) {
                val now = LocalDate.now()
                filtered = filtered.filter {
                    val gameDate = LocalDate.parse(it.releaseDate, DateTimeFormatter.ISO_DATE)
                    when (interval) {
                        DateInterval.LAST_WEEK -> gameDate.isAfter(now.minusWeeks(1))
                        DateInterval.LAST_30_DAYS -> gameDate.isAfter(now.minusDays(30))
                        DateInterval.LAST_90_DAYS -> gameDate.isAfter(now.minusDays(90))
                        else -> true
                    }
                }
            }

            emit(Resource.Success(filtered))
        } catch (e: Exception) {
            emit(Resource.Error(AppError.Unknown(e.message ?: "Error filtering games")))
        }
    }

    override suspend fun getGameById(id: Int): Resource<Game> {
        return try {
            simulateNetworkDelay()

            val game = dataSource.games.find { it.id == id }

            if (game != null) {
                Resource.Success(game)
            } else {
                Resource.Error(AppError.NotFound)
            }
        } catch (e: Exception) {
            Resource.Error(AppError.Unknown(e.message ?: "Error getting game"))
        }
    }
}
```


**Conceptos clave del MockRepository:**

**1. Flow builder con try-catch:**

```kotlin
fun getData(): Flow<Resource<T>> = flow {
    try {
        emit(Resource.Loading)
        // Operación que puede fallar
        val data = fetchData()
        emit(Resource.Success(data))
    } catch (e: Exception) {
        emit(Resource.Error(AppError.Unknown(e.message)))
    }
}
```


**2. Patrón de emisión estándar:**

```
Loading → (operación) → Success/Error
```

Siempre en este orden para que la UI pueda reaccionar correctamente.

**3. Filtrado local vs remoto:**

```kotlin
// Mock: Filtra TODOS los juegos en memoria
val filtered = mockGames.filter { condition }

// API (futuro): Servidor filtra y devuelve solo resultados
val response = api.getGames(filter = "RPG") // Solo envía RPGs
```


---

<u>✅ Resumen de la Fase 1</u>

Has completado la **capa de datos** con:

1. ✅ **Modelo Game** con propiedades completas
2. ✅ **Enums** (GameCategory, Platform, DateInterval) para type-safety
3. ✅ **Resource<T>** para manejar estados (Loading, Success, Error)
4. ✅ **AppError** con errores específicos y tipados
5. ✅ **MockGamesDataSource** con 12 juegos de prueba
6. ✅ **GamesRepository** (interfaz) que define el contrato
7. ✅ **MockGamesRepositoryImpl** que filtra en memoria

---

##  **FASE 2: Capa de Dominio - UseCases** {#fase-2-capa-de-dominio}

La capa de dominio contiene la **lógica de negocio** de la aplicación. Los UseCases son el corazón de esta capa y representan las acciones que un usuario puede realizar.

### ¿Qué son los UseCases?

Los **UseCases** (Casos de Uso) son clases que:

- ✅ Encapsulan lógica de negocio específica
- ✅ Orquestan llamadas a repositories
- ✅ Transforman y procesan datos según reglas de negocio
- ✅ Son independientes del framework (Android, iOS, Web)

### Arquitectura de nuestros UseCases

En este proyecto, los UseCases están **agrupados por funcionalidad**:

```
domain/usecase/
  ├─ GameUseCases.kt      ← Todos los casos de uso de Game
  ├─ LibraryUseCases.kt   ← (Futuro) Casos de uso de Library
  └─ UserUseCases.kt      ← (Futuro) Casos de uso de User
```

### Responsabilidades de UseCases vs Repository

```
┌─────────────────────────────────────────┐
│         GAMEUSECASES                    │
│  (Lógica de negocio + Transformación)   │
│                                         │
│  ✅ Ordenar por rating                  │
│  ✅ Ordenar por relevancia              │
│  ✅ Combinar múltiples filtros          │
│  ✅ Aplicar reglas de negocio           │
│  ✅ Transformar Resource<T>             │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│        GAMESREPOSITORY                  │
│     (Acceso a datos puro)               │
│                                         │
│  ✅ Obtener datos (mock/API)            │
│  ✅ Filtrar datos básico                │
│  ✅ Emitir Resource states              │
│  ❌ NO tiene lógica de ordenamiento     │
└─────────────────────────────────────────┘
```


---

###  Paso 2.1: Crear clase GameUseCases

Esta clase agrupa **todos los casos de uso relacionados con juegos**. Cada método representa una acción específica que el usuario puede realizar.

**Ubicación**: `app/src/main/java/com/pmdm/mygamestore/domain/usecase/GameUseCases.kt`

```kotlin
package com.pmdm.mygamestore.domain.usecase

/**
 *  Casos de uso agrupados para operaciones con juegos
 *
 * PATRÓN USE CASE:
 * - Encapsula lógica de negocio específica de la aplicación
 * - Orquesta llamadas a uno o más repositories
 * - Transforma datos del dominio para casos de uso específicos
 * - Es independiente del framework (Android, iOS, Web)
 *
 * ORGANIZACIÓN:
 * ✅ Una clase por entidad/funcionalidad (GameUseCases, LibraryUseCases, etc.)
 * ✅ Cada método es un caso de uso concreto
 * ✅ NO usamos operator invoke() (llamada directa al método)
 * ✅ Preparado para inyección de dependencias con Koin
 *
 * MANEJO DE RESOURCE:
 * ✅ Recibe Flow<Resource<List<Game>>> del repository
 * ✅ Aplica lógica solo a Resource.Success
 * ✅ Propaga Loading y Error sin modificar
 * ✅ Devuelve Flow<Resource<List<Game>>> al ViewModel
 *
 * @property gamesRepository Repository para acceder a los datos de juegos
 */
class GameUseCases(
    private val gamesRepository: GamesRepository
) {
     // ... contenido 
}
```

> [!TIP]
> > Puedes revisar el contenido en `presentation/domain/usercase/GameUseCases.kt`.


---

###  Conceptos clave de los UseCases

#### 1. **Flow.map { } para transformar Resource**

```kotlin
fun getTopRatedGames(): Flow<Resource<List<Game>>> {
    return repository.getAllGames()
        .map { resource ->
            when (resource) {
                is Resource.Success -> {
                    // Transformar SOLO los datos de Success
                    val filtered = resource.data.filter { it.rating >= 4.5 }
                    Resource.Success(filtered)
                }
                is Resource.Loading -> resource // Propagar
                is Resource.Error -> resource   // Propagar
            }
        }
}
```


**¿Por qué este patrón?**
- ✅ La lógica solo se aplica a datos exitosos
- ✅ Loading y Error se propagan sin modificar
- ✅ La UI recibe estados correctos

#### 2. **sortedBy vs sortedByDescending**

```kotlin
// Orden ascendente (menor a mayor)
games.sortedBy { it.price }
// Resultado: [$14.99, $19.99, $29.99, $39.99]

// Orden descendente (mayor a menor)
games.sortedByDescending { it.rating }
// Resultado: [5.0, 4.9, 4.8, 4.7]
```


#### 3. **filter para filtrar elementos**

```kotlin
// Filtrar juegos con rating >= 4.5
val topGames = games.filter { it.rating >= 4.5 }

// Filtrar por múltiples condiciones (AND)
val filtered = games.filter { 
    it.rating >= 4.0 && it.price <= 40.0 
}
```


#### 4. **take() para limitar resultados**

```kotlin
// Tomar los primeros 5 elementos
val top5 = games.take(5)

// Tomar los últimos 3
val last3 = games.takeLast(3)

// Tomar mientras se cumpla condición
val expensive = games.takeWhile { it.price > 50.0 }
```


#### 5. **Diferencia entre suspend fun y Flow**

```kotlin
// suspend fun: Devuelve UN valor
suspend fun getGameById(id: Int): Resource<Game>

// Flow: Puede emitir MÚLTIPLES valores en el tiempo
fun getAllGames(): Flow<Resource<List<Game>>>
```

---

###  Flujo completo de un caso de uso con Resource

```
1. Usuario escribe "witcher" en búsqueda
   │
   ▼
2. HomeScreen → viewModel.onSearchQueryChange("witcher")
   │
   ▼
3. HomeViewModel → gameUseCases.searchGames("witcher")
   │
   ▼
4. GameUseCases → gamesRepository.searchGames("witcher")
   │                    .map { ordenar por relevancia }
   ▼
5. MockGamesRepositoryImpl:
   │  emit(Resource.Loading)          ← UI muestra spinner
   │  delay(800)                       ← Simula red
   │  val filtered = mockGames.filter()
   │  emit(Resource.Success(filtered)) ← UI muestra juegos
   ▼
6. GameUseCases.map:
   │  when (Resource.Success) {
   │    ordenar por relevancia
   │    devolver Resource.Success(sorted)
   │  }
   ▼
7. HomeViewModel.collect:
   │  when (Resource.Success) {
   │    _uiState.update { games = result.data }
   │  }
   ▼
8. HomeScreen recompone con nuevos datos
```

<u>✅ Resumen de la Fase 2</u>

Se ha completado la **capa de dominio** con:

1. ✅ **Clase GameUseCases** con 10 casos de uso:
2. ✅ **Lógica de negocio** implementada.

---

##  **FASE 3: Capa de Presentación - ViewModel** {#fase-3-capa-de-presentación}

La capa de presentación gestiona el **estado de la UI** y coordina la lógica de presentación. El ViewModel es el componente central que conecta la UI con los casos de uso.

### ¿Qué es un ViewModel?

El **ViewModel** en MVVM:

- ✅ Gestiona el estado de la UI (HomeUiState)
- ✅ Sobrevive a cambios de configuración (rotación de pantalla)
- ✅ Coordina casos de uso (GameUseCases)
- ✅ Transforma datos del dominio para la UI
- ✅ Maneja eventos del usuario (clicks, búsquedas)
- ✅ NO tiene referencias a Views/Composables (evita memory leaks)

### Arquitectura MVVM

```
┌────────────────────────────────────────┐
│          VIEW (UI)                     │
│      HomeScreen (Composable)           │
│                                        │
│  - Observa uiState                     │
│  - Renderiza según estado              │
│  - Emite eventos al ViewModel          │
└──────────────┬─────────────────────────┘
               │ collectAsState()
               │ viewModel.onEvent()
┌──────────────▼─────────────────────────┐
│         VIEWMODEL                      │
│        HomeViewModel                   │
│                                        │
│  - StateFlow<HomeUiState>              │
│  - Coordina GameUseCases               │
│  - Maneja Resource states              │
│  - Actualiza UI state                  │
└──────────────┬─────────────────────────┘
               │ gameUseCases.method()
┌──────────────▼─────────────────────────┐
│         USE CASES                      │
│        GameUseCases                    │
│                                        │
│  - Lógica de negocio                   │
│  - Transforma datos                    │
└────────────────────────────────────────┘
```


---

###  Paso 3.1: Crear HomeUiState

El estado UI es un **data class inmutable** que representa TODO lo que la UI necesita para renderizarse.

**Ubicación**: `app/src/main/java/com/pmdm/mygamestore/presentation/viewmodel/HomeViewModel.kt`

```kotlin
package com.pmdm.mygamestore.presentation.viewmodel

/**
 * Clase que modela el estado de la interfaz de usuario para la pantalla principal.
 *
 * Proporciona información sobre los juegos cargados, el estado de la carga,
 * mensajes de error y el estado actual de la búsqueda y los filtros aplicados.
 *
 * Propiedades:
 * - `games`: Lista de juegos disponibles en la interfaz.
 * - `isLoading`: Indica si el contenido está en proceso de ser cargado.
 * - `errorMessage`: Mensaje de error devuelto en caso de fallo.
 * - `username`: Nombre del usuario actualmente autenticado.
 *
 * Opciones de búsqueda y filtros:
 * - `isSearchMode`: Indica si el modo de búsqueda está activado.
 * - `isFilterVisible`: Indica si el panel de filtros está visible.
 * - `searchQuery`: Texto de búsqueda introducido por el usuario.
 * - `selectedCategory`: Categoría seleccionada para filtrar juegos.
 * - `selectedPlatform`: Plataforma seleccionada para filtrar los juegos.
 * - `selectedInterval`: Intervalo de fechas seleccionado para filtrar juegos.
 */
data class HomeUiState(
    val games: List<Game> = emptyList(),
    val isLoading: Boolean = false,
    val errorMessage: String? = null,
    val username: String? = null,

    // Búsqueda y Filtros
    val isSearchMode: Boolean = false,
    val isFilterVisible: Boolean = false,
    val searchQuery: String = "",
    val selectedCategory: GameCategory = GameCategory.ALL,
    val selectedPlatform: PlatformEnum = PlatformEnum.ALL,
    val selectedInterval: DateInterval = DateInterval.ALL_TIME,
)
```


**Conceptos del Estado UI:**

**1. ¿Por qué data class?**
```kotlin
// data class genera automáticamente:
val state1 = HomeUiState(games = listOf())
val state2 = state1.copy(isLoading = true) // ✅ Copia con cambios

// equals(): compara por contenido
state1 == state2 // false (isLoading es diferente)

// toString(): para debugging
println(state1) // HomeUiState(games=[], isLoading=false, ...)
```


**2. Single Source of Truth:**
```kotlin
// ❌ MAL: Estado disperso
var games: List<Game> = emptyList()
var isLoading = false
var error: String? = null
// Difícil de sincronizar

// ✅ BIEN: Estado centralizado
val uiState = HomeUiState(
    games = emptyList(),
    isLoading = false,
    errorMessage = null
)
```


---

###  Paso 3.2: Crear HomeViewModel

El ViewModel coordina toda la lógica de la pantalla Home.

```kotlin
/**
 * ViewModel para gestionar el estado de la pantalla principal, específicamente para cargar,
 * filtrar y gestionar una lista de juegos, así como también administrar elementos relacionados
 * con la sesión del usuario.
 *
 * Este ViewModel encapsula la lógica de negocio necesaria para interactuar con los datos de juegos,
 * incluidos filtros, modos de búsqueda y control de errores. Además, interactúa con un gestor de sesión
 * para manejar datos del usuario como el nombre de usuario.
 *
 * Principales responsabilidades:
 * - Gestionar el estado inmutable y mutable de la vista utilizando `StateFlow`.
 * - Cargar los juegos aplicando diferentes tipos de filtros, búsqueda y criterios temporales.
 * - Soportar funcionalidades de gestión, incluyendo modos de búsqueda, visibilidad de filtros y limpieza de errores.
 * - Administrar estados de carga y manejo de errores, proporcionando mensajes de error descriptivos basados en diferentes fallos.
 *
 * Dependencias internas utilizadas:
 * - `GamesRepository`: Para obtener la lista de juegos y aplicar filtros sobre estos.
 * - `GameUseCases`: Un conjunto de casos de uso relacionados con juegos.
 * - `SessionManager`: Para gestionar información de la sesión de usuario, como el nombre de usuario.
 *
 * Métodos principales:
 * - `loadGames`: Carga y filtra juegos según los criterios actuales.
 * - `onSearchQueryChange`: Actualiza la query de búsqueda y recarga los juegos.
 * - `toggleSearchMode`: Activa o desactiva el modo de búsqueda y ajusta otros estados con base en esta acción.
 * - `toggleFilterVisibility`: Muestra u oculta el panel de filtros.
 * - `onCategorySelected`, `onPlatformSelected`, `onIntervalSelected`: Aplica filtros específicos y recarga los juegos.
 * - `refreshGames`: Recarga la lista de juegos, útil para acciones como pull-to-refresh.
 * - `clearError`: Limpia cualquier mensaje de error activo.
 * - `clearAllFilters`: Reinicia todos los filtros a sus valores por defecto y recarga los juegos.
 *
 * Esta clase promueve la separación de responsabilidades y coordina entre la capa de datos
 * (repositorios y casos de uso) y la capa de presentación (UI).
 */
class HomeViewModel(
    context: Context
) : ViewModel() {

    //  Dependencias instanciadas directamente (temporal, antes de Koin)
    private val gamesRepository: GamesRepository = MockGamesRepositoryImpl()
    private val gameUseCases = GameUseCases(gamesRepository)
    private val sessionManager: SessionManager = SessionManagerImpl(context)

    //  Estado privado mutable
    private val _uiState = MutableStateFlow(HomeUiState())

    //  Estado público inmutable
    val uiState: StateFlow<HomeUiState> = _uiState.asStateFlow()

    init {
        loadUsername()
        loadGames()
    }

    private fun loadUsername() {
        viewModelScope.launch {
            sessionManager.getUsername()
                .catch { exception ->
                    println("Error loading username: ${exception.message}")
                }
                .collect { username ->
                    _uiState.update { it.copy(username = username) }
                }
        }
    }

    fun loadGames() {
        viewModelScope.launch {
            val currentState = _uiState.value

            val gamesFlow = gameUseCases.getFilteredGames(
                query = currentState.searchQuery,
                category = currentState.selectedCategory,
                platform = currentState.selectedPlatform,
                interval = currentState.selectedInterval
            )

            gamesFlow.collect { resource ->
                when (resource) {
                    is Resource.Loading -> {
                        _uiState.update {
                            it.copy(
                                isLoading = true,
                                errorMessage = null
                            )
                        }
                    }

                    is Resource.Success -> {
                        _uiState.update {
                            it.copy(
                                games = resource.data,
                                isLoading = false,
                                errorMessage = null
                            )
                        }
                    }

                    is Resource.Error -> {
                        val errorMsg = when (resource.error) {
                            is AppError.NetworkError ->
                                "No internet connection. Please check your network."
                            is AppError.NotFound ->
                                "No games found."
                            is AppError.DatabaseError ->
                                "Database error. Please try again."
                            is AppError.Unauthorized ->
                                "You need to login to access this content."
                            is AppError.ValidationError ->
                                resource.error.message
                            is AppError.Unknown ->
                                resource.error.message
                        }

                        _uiState.update {
                            it.copy(
                                isLoading = false,
                                errorMessage = errorMsg
                            )
                        }
                    }
                }
            }
        }
    }

    fun onSearchQueryChange(query: String) {
        _uiState.update { it.copy(searchQuery = query) }
        loadGames()
    }

    fun toggleSearchMode() {
        _uiState.update {
            val newSearchMode = !it.isSearchMode
            it.copy(
                isSearchMode = newSearchMode,
                // Si abrimos búsqueda, cerramos filtros
                isFilterVisible = if (newSearchMode) false else it.isFilterVisible,
                // Si cerramos la búsqueda, limpiamos la query
                searchQuery = if (!newSearchMode) "" else it.searchQuery
            )
        }
        // Si acabamos de limpiar la búsqueda al cerrar el modo, recargamos juegos
        if (!_uiState.value.isSearchMode) {
            loadGames()
        }
    }

    fun toggleFilterVisibility() {
        _uiState.update {
            it.copy(isFilterVisible = !it.isFilterVisible)
        }
    }

    fun onCategorySelected(category: GameCategory) {
        _uiState.update {
            it.copy(selectedCategory = category)
        }
        loadGames()
    }

    fun onPlatformSelected(platform: PlatformEnum) {
        _uiState.update {
            it.copy(selectedPlatform = platform)
        }
        loadGames()
    }

    fun onIntervalSelected(interval: DateInterval) {
        _uiState.update {
            it.copy(selectedInterval = interval)
        }
        loadGames()
    }

    fun refreshGames() {
        loadGames()
    }

    fun clearError() {
        _uiState.update { it.copy(errorMessage = null) }
    }

    fun clearAllFilters() {
        _uiState.update {
            it.copy(
                searchQuery = "",
                selectedCategory = GameCategory.ALL,
                selectedPlatform = PlatformEnum.ALL,
                selectedInterval = DateInterval.ALL_TIME
            )
        }
        loadGames()
    }
}
```


---

###  Paso 3.3: Crear HomeViewModelFactory

El Factory es necesario porque HomeViewModel necesita Context, que no se puede pasar directamente.

```kotlin
/**
 *  Factory para crear HomeViewModel
 *
 * PROPÓSITO:
 * - ViewModel necesita Context para SessionManager
 * - ViewModelProvider.Factory permite pasar parámetros al constructor
*
* @param context Contexto de Android
 */
class HomeViewModelFactory(
    private val context: Context
) : ViewModelProvider.Factory {

    @Suppress("UNCHECKED_CAST")
    override fun <T : ViewModel> create(modelClass: Class<T>): T {
        if (modelClass.isAssignableFrom(HomeViewModel::class.java)) {
            return HomeViewModel(context) as T
        }
        throw IllegalArgumentException("Unknown ViewModel class: ${modelClass.name}")
    }
}
```


---

###  Conceptos clave del ViewModel

#### 1. **StateFlow vs MutableStateFlow**

```kotlin
// Privado: Solo el ViewModel puede modificar
private val _uiState = MutableStateFlow(HomeUiState())

// Público: La UI solo puede observar (read-only)
val uiState: StateFlow<HomeUiState> = _uiState.asStateFlow()
```


**¿Por qué este patrón?**
- ✅ Encapsulación: La UI no puede modificar el estado
- ✅ Unidirectional Data Flow: Solo el ViewModel actualiza
- ✅ Previene bugs: Cambios solo desde un lugar

#### 2. **update { } vs value =**

```kotlin
// ❌ Menos seguro en concurrencia
_uiState.value = _uiState.value.copy(isLoading = true)

// ✅ Thread-safe, atómico
_uiState.update { it.copy(isLoading = true) }
```


**update()** garantiza que:
- Las actualizaciones son atómicas
- No se pierde ningún cambio en concurrencia
- Sintaxis más limpia

#### 3. **viewModelScope**

```kotlin
// ✅ Se cancela automáticamente cuando ViewModel se destruye
viewModelScope.launch {
    // Operaciones asíncronas
}

// ❌ No uses GlobalScope (no se cancela nunca)
GlobalScope.launch { ... }
```


**Ventajas de viewModelScope:**
- Vinculado al ciclo de vida del ViewModel
- Se cancela automáticamente en onCleared()
- Previene memory leaks

#### 4. **Flow.collect vs Flow.collectLatest**

```kotlin
// collect: Procesa cada emisión completa
flow.collect { value ->
    // Se ejecuta para cada valor
}

// collectLatest: Cancela emisión anterior si llega una nueva
flow.collectLatest { value ->
    // Solo procesa el valor más reciente
    // Útil para búsquedas en tiempo real
}
```


#### 5. **catch operator para manejo de errores**

```kotlin
flow
    .catch { exception ->
        // Maneja errores en el Flow
        emit(defaultValue)
    }
    .collect { value ->
        // Procesar valor
    }
```


---

###  Flujo completo de datos con Resource

```
┌─────────────────────────────────────────────────┐
│  1. Usuario escribe "zelda" en búsqueda         │
└────────────────┬────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────┐
│  2. HomeScreen llama:                           │
│     viewModel.onSearchQueryChange("zelda")      │
└────────────────┬────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────┐
│  3. HomeViewModel:                              │
│     _uiState.update { searchQuery = "zelda" }   │
│     loadGames()                                 │
└────────────────┬────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────┐
│  4. loadGames() determina UseCase:              │
│     gameUseCases.searchGames("zelda")           │
└────────────────┬────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────┐
│  5. GameUseCases:                               │
│     gamesRepository.searchGames("zelda")        │
│     .map { ordenar por relevancia }             │
└────────────────┬────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────┐
│  6. MockGamesRepositoryImpl:                    │
│     emit(Resource.Loading)                      │
│     delay(800)                                  │
│     val filtered = mockGames.filter()           │
│     emit(Resource.Success(filtered))            │
└────────────────┬────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────┐
│  7. GameUseCases.map:                           │
│     when (Resource.Success) {                   │
│       ordenar por relevancia                    │
│       Resource.Success(sorted)                  │
│     }                                           │
└────────────────┬────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────┐
│  8. HomeViewModel.collect:                      │
│     when (resource) {                           │
│       Loading → isLoading = true                │
│       Success → games = resource.data           │
│       Error → errorMessage = ...                │
│     }                                           │
└────────────────┬────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────┐
│  9. HomeScreen recompone:                       │
│     val uiState by viewModel.uiState            │
│                    .collectAsState()            │
│     LazyVerticalGrid(uiState.games)             │
└─────────────────────────────────────────────────┘
```


---

<u>✅ Resumen de la Fase 3</u>

Has completado la **capa de presentación - ViewModel** con:

1. ✅ **HomeUiState** con todos los datos necesarios para la UI:

    - Lista de juegos
    - Estados de loading y error
    - Username del usuario
    - Filtros activos (búsqueda, categoría, plataforma, intervalo)

2. ✅ **HomeViewModel** con funcionalidades completas:

    - Gestión de estado con StateFlow
    - Coordinación de GameUseCases
    - Manejo de Resource (Loading, Success, Error)
    - Eventos del usuario (búsqueda, filtros)
    - Integración con SessionManager
    - Prioridad de filtros lógica

3. ✅ **HomeViewModelFactory** temporal para inyección de Context
4. ✅ **Manejo robusto de errores** con mensajes específicos por tipo


## **FASE 4: Componentes UI Reutilizables** {#fase-4-componentes-ui}

Esta fase se centra en crear componentes Compose reutilizables, stateless y bien organizados. Un componente **stateless** es aquel que no gestiona su propio estado, sino que lo recibe por parámetros, lo que facilita enormemente su testeo y reutilización en diferentes partes de la app.

---

!!! info "Dependencia de Coil y Material Icons Extended"
    Para esta fase necesitamos dos librerías fundamentales:
    1. **Coil**: Para cargar las imágenes de los juegos de forma asíncrona y eficiente.
    2. **Material Icons Extended**: Para acceder a una biblioteca más amplia de iconos (como el de Windows para PC o SearchOff).

    **Ubicación**: `app/build.gradle.kts`
    ```kotlin
    dependencies {
        implementation("io.coil-kt:coil-compose:2.5.0")
        implementation("androidx.compose.material:material-icons-extended:1.7.6")
    }
    ```

---

### 1. GameCard: La tarjeta del catálogo
Es el componente visual más importante. Debe ser atractivo y mostrar información clave de un vistazo sin saturar al usuario.

**¿Qué aprende el alumno aquí?**
- **HorizontalPager**: Para crear carruseles de imágenes deslizables.
- **Mapeo Dinámico**: Traducir datos del modelo (`slugs`) a elementos visuales (`Icons`).
- **Diseño Adaptativo**: Controlar el desbordamiento de texto con `maxLines` y `Ellipsis`.

```kotlin
@Composable
fun GameCard(
    game: Game,
    onClick: () -> Unit,
    modifier: Modifier = Modifier
) {
    Card(
        modifier = modifier
            .fillMaxWidth()
            .height(260.dp) // Altura optimizada para el grid
            .clickable(onClick = onClick),
        elevation = CardDefaults.cardElevation(defaultElevation = 4.dp)
    ) {
        Column {
            // Carrusel de imágenes (Uso de HorizontalPager)
            // ... (Ver implementación completa en el repositorio)
            
            // Información del juego
            Column(modifier = Modifier.padding(12.dp)) {
                Row(
                    modifier = Modifier.fillMaxWidth(),
                    horizontalArrangement = Arrangement.SpaceBetween
                ) {
                    // Iconos de Plataforma (Windows, PS, Xbox...)
                    PlatformIconsRow(platforms = game.platforms)
                    // Rating con estrella
                    // ...
                }
                // Título (Limitado a 1 línea para mantener simetría)
                Text(text = game.title, maxLines = 1, overflow = TextOverflow.Ellipsis)
            }
        }
    }
}
```
> [!TIP]
> Puedes revisar la implementación completa del carrusel y el mapeo de iconos en `presentation/ui/componentes/GameCard.kt`.

---

### 2. FilterSystem: Gestión de filtros compleja
Para evitar ocupar demasiado espacio, usamos una fila única de chips que despliega un panel inferior (**ModalBottomSheet**).

**Propósito pedagógico:**
- **UX Limpia**: No abrumar al usuario con 3 filas de filtros.
- **Interacción Avanzada**: Uso de `ModalBottomSheet` para selecciones cómodas.
- **Feedback Visual**: Los chips cambian de color e incluyen un icono de "check" cuando están activos.

```kotlin
@Composable
fun FilterSystem(
    selectedCategory: GameCategory,
    onCategorySelected: (GameCategory) -> Unit,
    // ... otros filtros
) {
    // Fila horizontal de chips compactos
    LazyRow(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
        item {
            CompactFilterChip(
                label = "Categoría",
                isSelected = selectedCategory != GameCategory.ALL,
                onClick = { /* Abrir BottomSheet */ }
            )
        }
        // ... otros chips
    }

    // El BottomSheet se encarga de mostrar las opciones sin tapar el catálogo
    if (showSheet) {
        ModalBottomSheet(onDismissRequest = { showSheet = false }) {
            // Lista de opciones para elegir
        }
    }
}
```

---

### 3. Estados de la Interfaz (UX de calidad)
Una aplicación profesional siempre informa al usuario de lo que está pasando.

- **LoadingIndicator**: Un spinner centrado para evitar una pantalla blanca durante la carga.
- **ErrorMessage**: Incluye un botón de "Retry" para que el usuario pueda recuperarse de un fallo de red.
- **EmptyState**: Se muestra cuando no hay resultados (ej: al buscar algo que no existe), permitiendo limpiar los filtros con un solo clic.

```kotlin
@Composable
fun EmptyState(
    message: String = "No games found",
    onClearFilters: (() -> Unit)? = null
) {
    Column(horizontalAlignment = Alignment.CenterHorizontally) {
        Icon(Icons.Default.SearchOff, modifier = Modifier.size(64.dp))
        Text(text = message)
        onClearFilters?.let {
            BotonGS(texto = "Clear Filters", onClick = it)
        }
    }
}
```

---

<u>✅ Resumen de la Fase 4</u>

Has creado una biblioteca de componentes robustos y educativos:

- ✅ **GameCard**: Con carrusel interactivo e iconos de plataforma inteligentes.
- ✅ **FilterSystem**: Compacto y basado en Material Design 3 (`ModalBottomSheet`).
- ✅ **GameGrid**: Organización eficiente en 2 columnas.
- ✅ **Gestión de Estados**: Feedback visual completo para Loading, Error y Empty.

---

## **FASE 5: HomeScreen Completa** {#fase-5-homescreen}

En esta fase final, uniremos todas las piezas que hemos construido: el **ViewModel** como cerebro de la pantalla, los **UseCases** como lógica de negocio, y la biblioteca de **Componentes UI** que diseñamos en la Fase 4.

La `HomeScreen` es el "orquestador". Su responsabilidad no es dibujar cada detalle, sino organizar los componentes y pasarles los datos y eventos necesarios.

### 1. La TopAppBar Dinámica (Búsqueda integrada)

Una de las características más profesionales de nuestra app es la barra superior que se transforma. En lugar de tener un campo de búsqueda estático que roba espacio, usamos un icono que activa el modo búsqueda.

**Conceptos clave para el alumno:**
- **Estado Visual**: Usamos `uiState.isSearchMode` para decidir qué versión de la `TopAppBar` mostrar.
- **Auto-focus**: Al abrir la búsqueda, usamos un `FocusRequester` para que el teclado aparezca automáticamente.

```kotlin
@Composable
fun HomeScreen() {
    // ... inicialización de ViewModel y State
    val focusRequester = remember { FocusRequester() }

    Scaffold(
        topBar = {
            if (uiState.isSearchMode) {
                // Barra con campo de texto (TextField)
                SearchTopBar(
                    query = uiState.searchQuery,
                    onQueryChange = { viewModel.onSearchQueryChange(it) },
                    onClose = { viewModel.toggleSearchMode() },
                    focusRequester = focusRequester
                )
            } else {
                // Barra estándar con título y botones de acción
                MainTopBar(
                    onToggleFilters = { viewModel.toggleFilterVisibility() },
                    onOpenSearch = { viewModel.toggleSearchMode() }
                )
            }
        },
        bottomBar = {
            // Navegación principal de la app
            BottomNavigationBar(currentRoute = AppRoutes.Home, onNavigate = { /* ... */ })
        }
    ) { innerPadding ->
        // Contenido...
    }
}
```

> [!TIP]
> Puedes revisar la implementación completa de la HomeScreen y el mapeo de iconos en `presentation/ui/screens/HomeScreen.kt`.

### 2. Animaciones de Visibilidad (`AnimatedVisibility`)

Para que la interfaz no sea "tosca", los filtros aparecen y desaparecen con una suave animación de persiana justo debajo de la barra superior.

```kotlin
AnimatedVisibility(
    visible = uiState.isFilterVisible,
    enter = expandVertically() + fadeIn(),
    exit = shrinkVertically() + fadeOut()
) {
    FilterSystem(
        selectedCategory = uiState.selectedCategory,
        // ... pasamos los callbacks del ViewModel
    )
}
```

### 3. El patrón "When" para la Gestión de Estados

El corazón del contenido de la pantalla utiliza la potencia de Kotlin para manejar los 4 estados fundamentales de cualquier aplicación moderna:

1.  **Cargando (`isLoading`)**: El usuario nunca debe ver una pantalla en blanco.
2.  **Error (`errorMessage`)**: Siempre debemos dar una explicación y un botón de reintento.
3.  **Vacío (`isEmpty`)**: Si no hay resultados (ej: una búsqueda sin éxito), ayudamos al usuario a limpiar sus filtros.
4.  **Éxito (`Success`)**: Mostramos la cuadrícula de juegos (`GameGrid`).

```kotlin
Box(modifier = Modifier.fillMaxSize()) {
    when {
        uiState.isLoading -> LoadingIndicator()
        uiState.errorMessage != null -> ErrorMessage(onRetry = { viewModel.refreshGames() })
        uiState.games.isEmpty() -> EmptyState(onClearFilters = { viewModel.clearAllFilters() })
        else -> GameGrid(games = uiState.games, onGameClick = { /* Navegar al detalle */ })
    }
}
```

### 4. Navegación e Integración Global

Finalmente, incluimos el componente **`BottomNavigationBar`**. Este componente es vital para la experiencia de usuario (UX), ya que permite saltar entre las secciones principales (Home, Biblioteca, Perfil) desde cualquier lugar.

**Consejo didáctico**: Fíjate cómo la `HomeScreen` no navega por sí misma, sino que utiliza el `LocalNavStack.current` para delegar la navegación a la infraestructura que definimos en el `NavGraph`.

---

<u>✅ Resumen de la Fase 5</u>

¡Enhorabuena! Has construido una pantalla de alta complejidad siguiendo los estándares de la industria:

- **Modular**: Componentes pequeños y reutilizables.
- **Reactiva**: Basada en estados de `HomeViewModel`.
- **UX Optimizada**: Búsqueda integrada, filtros inteligentes y animaciones fluidas.
- **Limpia**: Separación total entre la lógica de datos (Repository) y la visual (Compose).

Puedes revisar el código completo y los detalles de implementación en el archivo `presentation/ui/screens/HomeScreen.kt`.
