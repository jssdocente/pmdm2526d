# **5. Implementación de la Paantalla de HomeScreen con Catálogo de Juegos**

En esta 5ª parte del proyecto, vamos a implementar la pantalla de inicio de la aplicación, que mostrará un catálogo de juegos con funcionalidades de búsqueda, filtrado y navegación.

!!! tip "Repositorio de la Aplicación"
    El código fuente de la aplicación se encuentra en el repositorio de GitHub: [MyGameStore](https://github.com/jssdocente/MyGameStore)

## 📖 Índice

1. [Introducción](#introducción)
2. [Objetivos de Aprendizaje](#objetivos-de-aprendizaje)
3. [Arquitectura General](#arquitectura-general)
4. [Fase 1: Capa de Datos - Models y Repository](#fase-1-capa-de-datos)
5. [Fase 2: Capa de Dominio - UseCases](#fase-2-capa-de-dominio)
6. [Fase 3: Capa de Presentación - ViewModel](#fase-3-capa-de-presentación)
7. [Fase 4: Componentes UI Reutilizables](#fase-4-componentes-ui)
8. [Fase 5: HomeScreen Completa](#fase-5-homescreen)
9. [Fase 6: Integración con Navegación](#fase-6-navegación)
10. [Preparación para Inyección de Dependencias](#preparación-di)
11. [Resumen y Próximos Pasos](#resumen)

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

## 🎓 Objetivos de Aprendizaje {#objetivos-de-aprendizaje}

Al completar esta parte del proyecto, habrás aprendido:

### 🏗️ Arquitectura y Patrones

- ✨ Implementar **Clean Architecture** con capas separadas
- ✨ Aplicar el **patrón Repository** para abstracción de datos
- ✨ Crear **UseCases agrupados** por funcionalidad (sin operator invoke)
- ✨ Implementar **MVVM pattern** con ViewModel

### 📊 Gestión de Estado

- ✨ Gestionar **estado complejo** con StateFlow
- ✨ Usar **MutableStateFlow** vs **StateFlow** correctamente
- ✨ Combinar múltiples flujos de datos con **Flow operators**
- ✨ Manejar estados: Loading, Success, Empty, Error

### 🎨 Jetpack Compose

- ✨ Crear **componentes UI reutilizables** y stateless
- ✨ Trabajar con **LazyVerticalGrid** para listas eficientes
- ✨ Implementar **búsqueda reactiva** en tiempo real
- ✨ Usar **LaunchedEffect** para side-effects
- ✨ Integrar **Coil** para carga de imágenes

### 🧭 Navegación

- ✨ Implementar **navegación con parámetros** (gameId)
- ✨ Crear **BottomNavigationBar** funcional
- ✨ Gestionar **backStack** correctamente

### 🔧 Preparación para DI

- ✨ Estructurar código listo para **Inyección de Dependencias con Koin**
- ✨ Entender cuándo instanciar directamente vs. inyectar por constructor

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
 * @property title Título del juego
 * @property description Descripción detallada del juego
 * @property imageUrl URL de la imagen de portada (cargada con Coil)
 * @property price Precio en formato decimal (ej: 59.99)
 * @property rating Valoración de 0.0 a 5.0 (ej: 4.8)
 * @property releaseDate Fecha de lanzamiento en formato ISO (yyyy-MM-dd): "2024-01-15"
 * @property category Categoría principal del juego (ACTION, RPG, etc.)
 * @property platform Plataforma en la que está disponible
 * @property genres Lista de géneros asociados (["RPG", "Open World", "Fantasy"])
 */
@Serializable
data class Game(
    val id: Int,
    val title: String,
    val description: String,
    val imageUrl: String,
    val price: Double,
    val rating: Double,
    val releaseDate: String,
    val category: GameCategory,
    val platform: Platform,
    val genres: List<String>
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

**Ubicación**: `app/src/main/java/com/pmdm/mygamestore/data/local/MockGamesDataSource.kt`

```kotlin
package com.pmdm.mygamestore.data.local

import com.pmdm.mygamestore.domain.model.Game
import com.pmdm.mygamestore.domain.model.GameCategory
import com.pmdm.mygamestore.domain.model.Platform

/**
 *  Fuente de datos mock para desarrollo
 *
 * RESPONSABILIDAD:
 * - Proveer datos de prueba para desarrollo sin depender de API
 * - Simular respuesta de una base de datos o API
 *
 * VENTAJAS:
 * ✅ Separación de datos del repository
 * ✅ Desarrollo sin depender de backend
 * ✅ Fácil cambiar a fuente real (API, DB)
 * ✅ Reutilizable en tests
 * ✅ Datos centralizados
 *
 * MIGRACIÓN A PRODUCCIÓN:
 * Cuando conectes a API real:
 * 1. Crear GamesApiService con Retrofit
 * 2. Crear GamesRepositoryImpl que use GamesApiService
 * 3. MockGamesRepositoryImpl queda solo para desarrollo/testing
 *
 * ```
 * // Desarrollo
 * val repository = MockGamesRepositoryImpl(MockGamesDataSource)
 *
 * // Producción
 * val repository = GamesRepositoryImpl(GamesApiService)
 * ```
 */
object MockGamesDataSource {
    
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
        Game(
            id = 1,
            title = "The Witcher 3: Wild Hunt",
            description = "An epic open-world RPG adventure set in a fantasy universe. Hunt monsters, make choices that matter, and explore a vast world filled with quests.",
            imageUrl = "https://picsum.photos/400/600?random=1",
            price = 39.99,
            rating = 4.8,
            releaseDate = "2024-01-15",
            category = GameCategory.RPG,
            platform = Platform.PC,
            genres = listOf("RPG", "Open World", "Fantasy")
        ),
        Game(
            id = 2,
            title = "God of War Ragnarök",
            description = "Continue Kratos and Atreus' Norse adventure in this breathtaking sequel. Epic battles, emotional storytelling, and stunning visuals await.",
            imageUrl = "https://picsum.photos/400/600?random=2",
            price = 59.99,
            rating = 4.9,
            releaseDate = "2024-02-01",
            category = GameCategory.ACTION,
            platform = Platform.PLAYSTATION,
            genres = listOf("Action", "Adventure", "Mythology")
        ),
        Game(
            id = 3,
            title = "Halo Infinite",
            description = "Master Chief returns in this epic FPS adventure. Experience the next chapter of the legendary Halo saga with new gameplay and multiplayer.",
            imageUrl = "https://picsum.photos/400/600?random=3",
            price = 49.99,
            rating = 4.5,
            releaseDate = "2023-12-10",
            category = GameCategory.ACTION,
            platform = Platform.XBOX,
            genres = listOf("FPS", "Sci-Fi", "Multiplayer")
        ),
        Game(
            id = 4,
            title = "The Legend of Zelda: TOTK",
            description = "Explore Hyrule like never before in this groundbreaking adventure. Discover new abilities, solve puzzles, and uncover ancient secrets.",
            imageUrl = "https://picsum.photos/400/600?random=4",
            price = 59.99,
            rating = 5.0,
            releaseDate = "2024-01-20",
            category = GameCategory.ADVENTURE,
            platform = Platform.NINTENDO,
            genres = listOf("Adventure", "Puzzle", "Open World")
        ),
        Game(
            id = 5,
            title = "Stardew Valley",
            description = "Build your dream farm in this relaxing and addictive simulation. Grow crops, raise animals, go fishing, and become part of the community.",
            imageUrl = "https://picsum.photos/400/600?random=5",
            price = 14.99,
            rating = 4.7,
            releaseDate = "2023-11-05",
            category = GameCategory.SIMULATION,
            platform = Platform.MOBILE,
            genres = listOf("Simulation", "Farming", "Indie")
        ),
        Game(
            id = 6,
            title = "FIFA 24",
            description = "The ultimate football experience with realistic gameplay, updated rosters, and exciting new game modes for solo and multiplayer.",
            imageUrl = "https://picsum.photos/400/600?random=6",
            price = 69.99,
            rating = 4.2,
            releaseDate = "2024-02-10",
            category = GameCategory.SPORTS,
            platform = Platform.PC,
            genres = listOf("Sports", "Simulation", "Multiplayer")
        ),
        Game(
            id = 7,
            title = "Civilization VI",
            description = "Build an empire to stand the test of time. Lead your civilization from the Stone Age to the Information Age in this turn-based strategy masterpiece.",
            imageUrl = "https://picsum.photos/400/600?random=7",
            price = 29.99,
            rating = 4.6,
            releaseDate = "2023-10-20",
            category = GameCategory.STRATEGY,
            platform = Platform.PC,
            genres = listOf("Strategy", "Turn-Based", "Historical")
        ),
        Game(
            id = 8,
            title = "Tetris Effect",
            description = "Experience Tetris like never before with stunning visuals and an incredible soundtrack that reacts to your gameplay in real-time.",
            imageUrl = "https://picsum.photos/400/600?random=8",
            price = 19.99,
            rating = 4.4,
            releaseDate = "2024-01-05",
            category = GameCategory.PUZZLE,
            platform = Platform.MOBILE,
            genres = listOf("Puzzle", "Music", "Casual")
        ),
        Game(
            id = 9,
            title = "Elden Ring",
            description = "From FromSoftware and George R.R. Martin comes a dark fantasy epic. Explore a vast interconnected world filled with danger and mystery.",
            imageUrl = "https://picsum.photos/400/600?random=9",
            price = 59.99,
            rating = 4.9,
            releaseDate = "2024-01-25",
            category = GameCategory.RPG,
            platform = Platform.PLAYSTATION,
            genres = listOf("RPG", "Souls-like", "Dark Fantasy")
        ),
        Game(
            id = 10,
            title = "Forza Horizon 5",
            description = "Open-world racing in beautiful Mexico. Drive hundreds of the world's greatest cars through stunning environments and dynamic seasons.",
            imageUrl = "https://picsum.photos/400/600?random=10",
            price = 49.99,
            rating = 4.7,
            releaseDate = "2023-12-15",
            category = GameCategory.SPORTS,
            platform = Platform.XBOX,
            genres = listOf("Racing", "Open World", "Arcade")
        ),
        Game(
            id = 11,
            title = "Minecraft",
            description = "Create and explore infinite worlds. Build anything you can imagine in this blocky sandbox adventure that has captured millions of players.",
            imageUrl = "https://picsum.photos/400/600?random=11",
            price = 26.95,
            rating = 4.8,
            releaseDate = "2023-11-12",
            category = GameCategory.SIMULATION,
            platform = Platform.MOBILE,
            genres = listOf("Sandbox", "Survival", "Creative")
        ),
        Game(
            id = 12,
            title = "Call of Duty: MW3",
            description = "The iconic FPS returns with intense multiplayer action and a gripping campaign. Join the fight in the latest Modern Warfare installment.",
            imageUrl = "https://picsum.photos/400/600?random=12",
            price = 69.99,
            rating = 4.3,
            releaseDate = "2024-02-05",
            category = GameCategory.ACTION,
            platform = Platform.PC,
            genres = listOf("FPS", "Military", "Multiplayer")
        )
    )
}
```


---

###  Paso 1.5: Crear interfaz GamesRepository

El **patrón Repository** abstrae el origen de los datos. Definimos un contrato que cualquier implementación (mock o real) debe cumplir.

**Ubicación**: `app/src/main/java/com/pmdm/mygamestore/data/repository/GamesRepository.kt`

```kotlin
package com.pmdm.mygamestore.data.repository

import com.pmdm.mygamestore.domain.model.Game
import com.pmdm.mygamestore.domain.model.GameCategory
import com.pmdm.mygamestore.domain.model.DateInterval
import com.pmdm.mygamestore.domain.model.Platform
import com.pmdm.mygamestore.domain.model.Resource
import kotlinx.coroutines.flow.Flow

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
     *
     * IMPLEMENTACIONES:
     * - Mock: Devuelve MockGamesDataSource.games
     * - API: GET /api/games
     *
     * @return Flow que emite Resource con estados:
     *         - Loading: Mientras se obtienen los datos
     *         - Success: Con la lista completa de juegos
     *         - Error: Si falla la operación
     *
     * Ejemplo de uso:
     * ```
     * repository.getAllGames().collect { resource ->
     *     when (resource) {
     *         is Resource.Loading -> showLoader()
     *         is Resource.Success -> displayGames(resource.data)
     *         is Resource.Error -> showError(resource.error)
     *     }
     * }
     * ```
     */
    fun getAllGames(): Flow<Resource<List<Game>>>

    /**
     * Filtra juegos por categoría
     *
     * IMPLEMENTACIONES:
     * - Mock: Filtra mockGames en memoria
     * - API: GET /api/games?category=RPG
     *
     * @param category Categoría a filtrar (ACTION, RPG, etc.)
     * @return Flow<Resource<List<Game>>>
     */
    fun getGamesByCategory(category: GameCategory): Flow<Resource<List<Game>>>

    /**
     * Filtra juegos por intervalo de fecha de lanzamiento
     *
     * IMPLEMENTACIONES:
     * - Mock: Filtra mockGames por fecha en memoria
     * - API: GET /api/games?interval=LAST_WEEK
     *
     * @param interval Intervalo de tiempo (LAST_WEEK, LAST_30_DAYS, etc.)
     * @return Flow<Resource<List<Game>>>
     */
    fun getGamesByInterval(interval: DateInterval): Flow<Resource<List<Game>>>

    /**
     * Filtra juegos por plataforma
     *
     * IMPLEMENTACIONES:
     * - Mock: Filtra mockGames en memoria
     * - API: GET /api/games?platform=PLAYSTATION
     *
     * @param platform Plataforma deseada (PC, PLAYSTATION, etc.)
     * @return Flow<Resource<List<Game>>>
     */
    fun getGamesByPlatform(platform: Platform): Flow<Resource<List<Game>>>

    /**
     * Filtra juegos por géneros
     *
     * IMPLEMENTACIONES:
     * - Mock: Filtra mockGames en memoria
     * - API: GET /api/games?genres=RPG,Fantasy
     *
     * @param genres Lista de géneros a buscar
     * @return Flow<Resource<List<Game>>>
     */
    fun getGamesByGenres(genres: List<String>): Flow<Resource<List<Game>>>

    /**
     * Busca juegos por texto en título o descripción
     *
     * IMPLEMENTACIONES:
     * - Mock: Busca en mockGames con contains()
     * - API: GET /api/games/search?q=witcher
     *
     * @param query Texto a buscar (case-insensitive)
     * @return Flow<Resource<List<Game>>>
     */
    fun searchGames(query: String): Flow<Resource<List<Game>>>

    /**
     * Obtiene un juego específico por su ID
     *
     * IMPLEMENTACIONES:
     * - Mock: Busca en mockGames con find()
     * - API: GET /api/games/{id}
     *
     * @param id Identificador del juego
     * @return Resource con el juego o error NotFound
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

import com.pmdm.mygamestore.data.local.MockGamesDataSource
import com.pmdm.mygamestore.domain.model.AppError
import com.pmdm.mygamestore.domain.model.Game
import com.pmdm.mygamestore.domain.model.GameCategory
import com.pmdm.mygamestore.domain.model.DateInterval
import com.pmdm.mygamestore.domain.model.Platform
import com.pmdm.mygamestore.domain.model.Resource
import kotlinx.coroutines.delay
import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.flow.flow
import java.time.LocalDate
import java.time.format.DateTimeFormatter

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
 * ```
 * // MockGamesRepositoryImpl (esta clase)
 * fun searchGames(query) {
 *     val filtered = mockGames.filter { it.title.contains(query) } // ← Cliente filtra
 * }
 *
 * // GamesRepositoryImpl (futuro, con API)
 * fun searchGames(query) {
 *     val response = api.searchGames(query) // ← Servidor filtra
 * }
 * ```
 *
 * CUÁNDO USAR:
 * ✅ Desarrollo inicial sin API
 * ✅ Testing de UseCases y ViewModels
 * ✅ Demos y prototipos
 *
 * CUÁNDO NO USAR:
 * ❌ Producción con datos reales
 * ❌ Grandes volúmenes de datos (lento filtrar en cliente)
 */
class MockGamesRepositoryImpl : GamesRepository {

    /**
     *  Fuente de datos mock
     */
    private val dataSource = MockGamesDataSource

    /**
     * ⏱️ Simula delay de red
     *
     * En una app real, esto sería:
     * - Tiempo de respuesta del servidor (100-2000ms)
     * - Tiempo de lectura de base de datos (10-100ms)
     *
     * Beneficios de simular delay:
     * ✅ Testear estados de loading
     * ✅ Ver cómo se comporta la UI durante cargas
     * ✅ Simular condiciones de red lenta
     */
    private suspend fun simulateNetworkDelay() {
        delay(800) // 800ms
    }

    override fun getAllGames(): Flow<Resource<List<Game>>> = flow {
        try {
            // 1️⃣ Emitir estado Loading
            emit(Resource.Loading)
            
            // 2️⃣ Simular operación asíncrona (red/DB)
            simulateNetworkDelay()
            
            // 3️⃣ Obtener datos
            val games = dataSource.games
            
            // 4️⃣ Emitir Success con datos
            emit(Resource.Success(games))
            
        } catch (e: Exception) {
            // 5️⃣ Si algo falla, emitir Error
            emit(Resource.Error(AppError.Unknown(e.message ?: "Unknown error")))
        }
    }

    override fun getGamesByCategory(category: GameCategory): Flow<Resource<List<Game>>> = flow {
        try {
            emit(Resource.Loading)
            simulateNetworkDelay()
            
            // Filtrar en memoria (simula lo que haría la API)
            val filtered = if (category == GameCategory.ALL) {
                dataSource.games
            } else {
                dataSource.games.filter { it.category == category }
            }
            
            emit(Resource.Success(filtered))
            
        } catch (e: Exception) {
            emit(Resource.Error(AppError.Unknown(e.message ?: "Error filtering by category")))
        }
    }

    override fun getGamesByInterval(interval: DateInterval): Flow<Resource<List<Game>>> = flow {
        try {
            emit(Resource.Loading)
            simulateNetworkDelay()
            
            // Filtrar por fecha en memoria
            val now = LocalDate.now()
            val filtered = when (interval) {
                DateInterval.ALL_TIME -> dataSource.games
                
                DateInterval.LAST_WEEK -> dataSource.games.filter {
                    val gameDate = LocalDate.parse(it.releaseDate, DateTimeFormatter.ISO_DATE)
                    gameDate.isAfter(now.minusWeeks(1))
                }
                
                DateInterval.LAST_30_DAYS -> dataSource.games.filter {
                    val gameDate = LocalDate.parse(it.releaseDate, DateTimeFormatter.ISO_DATE)
                    gameDate.isAfter(now.minusDays(30))
                }
                
                DateInterval.LAST_90_DAYS -> dataSource.games.filter {
                    val gameDate = LocalDate.parse(it.releaseDate, DateTimeFormatter.ISO_DATE)
                    gameDate.isAfter(now.minusDays(90))
                }
            }
            
            emit(Resource.Success(filtered))
            
        } catch (e: Exception) {
            emit(Resource.Error(AppError.Unknown(e.message ?: "Error filtering by date")))
        }
    }

    override fun getGamesByPlatform(platform: Platform): Flow<Resource<List<Game>>> = flow {
        try {
            emit(Resource.Loading)
            simulateNetworkDelay()
            
            // Filtrar por plataforma en memoria
            val filtered = if (platform == Platform.ALL) {
                dataSource.games
            } else {
                dataSource.games.filter { it.platform == platform }
            }
            
            emit(Resource.Success(filtered))
            
        } catch (e: Exception) {
            emit(Resource.Error(AppError.Unknown(e.message ?: "Error filtering by platform")))
        }
    }

    override fun getGamesByGenres(genres: List<String>): Flow<Resource<List<Game>>> = flow {
        try {
            emit(Resource.Loading)
            simulateNetworkDelay()
            
            // Filtrar por géneros en memoria (OR: al menos uno coincide)
            val filtered = dataSource.games.filter { game ->
                game.genres.any { genre -> 
                    genres.any { it.equals(genre, ignoreCase = true) }
                }
            }
            
            emit(Resource.Success(filtered))
            
        } catch (e: Exception) {
            emit(Resource.Error(AppError.Unknown(e.message ?: "Error filtering by genres")))
        }
    }

    override fun searchGames(query: String): Flow<Resource<List<Game>>> = flow {
        try {
            emit(Resource.Loading)
            simulateNetworkDelay()
            
            // Buscar en memoria (título y descripción)
            val filtered = dataSource.games.filter { game ->
                game.title.contains(query, ignoreCase = true) ||
                game.description.contains(query, ignoreCase = true)
            }
            
            emit(Resource.Success(filtered))
            
        } catch (e: Exception) {
            emit(Resource.Error(AppError.Unknown(e.message ?: "Error searching games")))
        }
    }

    override suspend fun getGameById(id: Int): Resource<Game> {
        return try {
            simulateNetworkDelay()
            
            // Buscar por ID en memoria
            val game = dataSource.games.find { it.id == id }
            
            if (game != null) {
                Resource.Success(game)
            } else {
                // Juego no encontrado
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

### ✅ Resumen de la Fase 1

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

import com.pmdm.mygamestore.data.repository.GamesRepository
import com.pmdm.mygamestore.domain.model.Game
import com.pmdm.mygamestore.domain.model.GameCategory
import com.pmdm.mygamestore.domain.model.DateInterval
import com.pmdm.mygamestore.domain.model.Platform
import com.pmdm.mygamestore.domain.model.Resource
import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.flow.map

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
 * IMPORTANTE - Sin DI por ahora:
 * - Esta clase se instancia directamente en el ViewModel
 * - Cuando se implemente Koin, se recibirá por constructor en el ViewModel
 *
 * @property gamesRepository Repository para acceder a los datos de juegos
 */
class GameUseCases(
    private val gamesRepository: GamesRepository
) {

    /**
     *  UC-001: Obtiene todos los juegos del catálogo
     *
     * CASO DE USO:
     * - Usuario abre la app
     * - Usuario limpia todos los filtros
     * - Vista por defecto del catálogo
     *
     * LÓGICA DE NEGOCIO:
     * - Obtiene todos los juegos sin filtrar
     * - Sin ordenamiento adicional (orden natural)
     *
     * @return Flow<Resource<List<Game>>>
     *         - Loading: Mientras obtiene datos
     *         - Success: Con lista completa de juegos
     *         - Error: Si falla la operación
     *
     * Ejemplo de uso:
     * ```kotlin
     * gameUseCases.getAllGames().collect { resource ->
     *     when (resource) {
     *         is Resource.Loading -> showLoader()
     *         is Resource.Success -> displayGames(resource.data)
     *         is Resource.Error -> showError(resource.error)
     *     }
     * }
     * ```
     */
    fun getAllGames(): Flow<Resource<List<Game>>> {
        return gamesRepository.getAllGames()
    }

    /**
     *  UC-002: Filtra juegos por categoría y ordena por rating
     *
     * CASO DE USO:
     * - Usuario hace click en chip "RPG"
     * - Usuario navega a sección "Juegos de Acción"
     *
     * LÓGICA DE NEGOCIO:
     * - Repository filtra por categoría
     * - UseCase ordena por rating (mejor valorados primero)
     *
     * ¿Por qué ordenar por rating?
     * - Mejora experiencia: usuarios ven mejores juegos primero
     * - Aumenta probabilidad de compra
     * - Práctica común en tiendas (Steam, Epic, PlayStation Store)
     *
     * @param category Categoría a filtrar (ACTION, RPG, etc.)
     * @return Flow<Resource<List<Game>>> ordenados por rating descendente
     */
    fun getGamesByCategory(category: GameCategory): Flow<Resource<List<Game>>> {
        return gamesRepository.getGamesByCategory(category)
            .map { resource ->
                // Solo aplicar lógica si es Success
                when (resource) {
                    is Resource.Success -> {
                        // Ordenar por rating (mayor a menor)
                        val sortedGames = resource.data.sortedByDescending { it.rating }
                        Resource.Success(sortedGames)
                    }
                    is Resource.Loading -> resource // Propagar sin cambios
                    is Resource.Error -> resource   // Propagar sin cambios
                }
            }
    }

    /**
     *  UC-003: Filtra por intervalo de fecha y ordena por fecha
     *
     * CASOS DE USO:
     * - Sección "Novedades de la semana"
     * - Sección "Lanzamientos del mes"
     * - Filtro "Últimos 90 días"
     *
     * LÓGICA DE NEGOCIO:
     * - Repository filtra por intervalo de fecha
     * - UseCase ordena por fecha (más recientes primero)
     *
     * REGLAS DE NEGOCIO:
     * - LAST_WEEK: Últimos 7 días
     * - LAST_30_DAYS: Último mes
     * - LAST_90_DAYS: Últimos 3 meses
     * - ALL_TIME: Sin filtro de fecha
     *
     * @param interval Intervalo de tiempo
     * @return Flow<Resource<List<Game>>> ordenados por fecha descendente
     */
    fun getGamesInterval(interval: DateInterval): Flow<Resource<List<Game>>> {
        return gamesRepository.getGamesByInterval(interval)
            .map { resource ->
                when (resource) {
                    is Resource.Success -> {
                        // Ordenar por fecha de lanzamiento (más recientes primero)
                        val sortedGames = resource.data.sortedByDescending { it.releaseDate }
                        Resource.Success(sortedGames)
                    }
                    is Resource.Loading -> resource
                    is Resource.Error -> resource
                }
            }
    }

    /**
     *  UC-004: Filtra juegos por plataforma
     *
     * CASO DE USO:
     * - Usuario tiene PlayStation y solo quiere juegos compatibles
     * - Filtro de plataforma en la UI
     *
     * LÓGICA DE NEGOCIO:
     * - Repository filtra por plataforma
     * - Sin ordenamiento adicional
     *
     * MEJORA FUTURA:
     * - Ordenar por popularidad en esa plataforma
     * - Mostrar exclusivos primero
     *
     * @param platform Plataforma (PC, PLAYSTATION, XBOX, etc.)
     * @return Flow<Resource<List<Game>>>
     */
    fun getGamesByPlatform(platform: Platform): Flow<Resource<List<Game>>> {
        return gamesRepository.getGamesByPlatform(platform)
    }

    /**
     * ️ UC-005: Filtra juegos por géneros
     *
     * CASO DE USO:
     * - Usuario busca juegos con etiquetas específicas
     * - Búsqueda multi-género: "RPG" + "Open World"
     *
     * LÓGICA DE NEGOCIO:
     * - Filtro inclusivo (OR): Juego debe tener AL MENOS uno de los géneros
     * - No es exclusivo (AND): No requiere TODOS los géneros
     *
     * Ejemplo:
     * - Buscar ["RPG", "Fantasy"]
     * - "The Witcher" (RPG + Fantasy) → ✅ Incluido
     * - "Elden Ring" (RPG + Dark Fantasy) → ✅ Incluido
     * - "FIFA" (Sports) → ❌ Excluido
     *
     * @param genres Lista de géneros a buscar
     * @return Flow<Resource<List<Game>>>
     */
    fun getGamesByGenres(genres: List<String>): Flow<Resource<List<Game>>> {
        return gamesRepository.getGamesByGenres(genres)
    }

    /**
     *  UC-006: Busca juegos por texto y ordena por relevancia
     *
     * CASO DE USO:
     * - Usuario escribe "witcher" en la barra de búsqueda
     * - Búsqueda en tiempo real mientras escribe
     *
     * LÓGICA DE NEGOCIO:
     * - Si query está vacío → devuelve todos los juegos
     * - Si no → busca en título y descripción
     * - Ordena por RELEVANCIA (coincidencias en título primero)
     *
     * ORDENAMIENTO POR RELEVANCIA:
     * 1. Título contiene query (prioridad 2)
     * 2. Descripción contiene query (prioridad 1)
     * 3. Sin coincidencia (prioridad 0)
     *
     * ¿Por qué este orden?
     * - Título es más relevante que descripción
     * - Mejora experiencia del usuario
     * - Similar a buscadores como Google
     *
     * @param query Texto a buscar (case-insensitive)
     * @return Flow<Resource<List<Game>>> ordenados por relevancia
     *
     * Ejemplo:
     * ```kotlin
     * searchGames("god").collect { resource ->
     *     when (resource) {
     *         is Resource.Success -> {
     *             // "God of War" aparece primero (coincidencia en título)
     *             // Juegos con "god" en descripción aparecen después
     *         }
     *     }
     * }
     * ```
     */
    fun searchGames(query: String): Flow<Resource<List<Game>>> {
        // Si la búsqueda está vacía, devolver todos
        if (query.isBlank()) {
            return gamesRepository.getAllGames()
        }
        
        return gamesRepository.searchGames(query)
            .map { resource ->
                when (resource) {
                    is Resource.Success -> {
                        // Ordenar por relevancia
                        val sortedGames = resource.data.sortedByDescending { game ->
                            when {
                                game.title.contains(query, ignoreCase = true) -> 2
                                game.description.contains(query, ignoreCase = true) -> 1
                                else -> 0
                            }
                        }
                        Resource.Success(sortedGames)
                    }
                    is Resource.Loading -> resource
                    is Resource.Error -> resource
                }
            }
    }

    /**
     *  UC-007: Obtiene un juego específico por ID
     *
     * CASOS DE USO:
     * - Usuario hace click en juego → navega a DetailScreen
     * - Deep linking: abrir app directamente en un juego
     * - Compartir enlace de juego
     * - Notificación push sobre un juego específico
     *
     * LÓGICA DE NEGOCIO:
     * - Busca juego por ID único
     * - Devuelve Resource.Success con juego
     * - O Resource.Error con AppError.NotFound
     *
     * Es suspend porque:
     * - Puede requerir operaciones I/O (DB, API)
     * - No es Flow porque solo devuelve un valor
     *
     * @param id Identificador único del juego
     * @return Resource<Game>
     *         - Success: Con el juego encontrado
     *         - Error(NotFound): Si no existe
     *         - Error(Unknown): Si falla la operación
     *
     * Ejemplo:
     * ```kotlin
     * viewModelScope.launch {
     *     when (val result = gameUseCases.getGameById(5)) {
     *         is Resource.Success -> showDetails(result.data)
     *         is Resource.Error -> {
     *             when (result.error) {
     *                 is AppError.NotFound -> showNotFoundError()
     *                 else -> showGenericError()
     *             }
     *         }
     *     }
     * }
     * ```
     */
    suspend fun getGameById(id: Int): Resource<Game> {
        return gamesRepository.getGameById(id)
    }

    /**
     * ⭐ UC-008: Obtiene juegos mejor valorados
     *
     * CASO DE USO:
     * - Sección "Top Rated" en home
     * - Sección "Mejor valorados de la semana"
     * - Recomendaciones de alta calidad
     *
     * LÓGICA DE NEGOCIO:
     * - Obtiene todos los juegos
     * - Filtra: rating >= minRating (por defecto 4.5)
     * - Ordena: por rating descendente
     *
     * ¿Por qué rating 4.5?
     * - Estándar industria para "excelente"
     * - Steam: 4.5/5 = "Overwhelmingly Positive"
     * - PlayStation Store destaca juegos 4.5+
     *
     * @param minRating Rating mínimo (por defecto 4.5 de 5.0)
     * @return Flow<Resource<List<Game>>>
     */
    fun getTopRatedGames(minRating: Double = 4.5): Flow<Resource<List<Game>>> {
        return gamesRepository.getAllGames()
            .map { resource ->
                when (resource) {
                    is Resource.Success -> {
                        val topGames = resource.data
                            .filter { it.rating >= minRating }
                            .sortedByDescending { it.rating }
                        Resource.Success(topGames)
                    }
                    is Resource.Loading -> resource
                    is Resource.Error -> resource
                }
            }
    }

    /**
     *  UC-009: Filtra juegos por rango de precio
     *
     * CASO DE USO:
     * - Usuario busca juegos baratos (< $20)
     * - Sección "Juegos en oferta"
     * - Filtro de presupuesto
     *
     * LÓGICA DE NEGOCIO:
     * - Filtra: price <= maxPrice
     * - Ordena: por precio ascendente (más baratos primero)
     *
     * MEJORA FUTURA:
     * - Agregar minPrice para rangos ($20-$40)
     * - Calcular descuentos
     * - Destacar ofertas limitadas
     *
     * @param maxPrice Precio máximo en dólares
     * @return Flow<Resource<List<Game>>>
     *
     * Ejemplo:
     * ```kotlin
     * // Juegos de menos de $30
     * gameUseCases.getGamesByPriceRange(30.0).collect { resource ->
     *     when (resource) {
     *         is Resource.Success -> {
     *             println("${resource.data.size} juegos económicos")
     *         }
     *     }
     * }
     * ```
     */
    fun getGamesByPriceRange(maxPrice: Double): Flow<Resource<List<Game>>> {
        return gamesRepository.getAllGames()
            .map { resource ->
                when (resource) {
                    is Resource.Success -> {
                        val filtered = resource.data
                            .filter { it.price <= maxPrice }
                            .sortedBy { it.price } // Más baratos primero
                        Resource.Success(filtered)
                    }
                    is Resource.Loading -> resource
                    is Resource.Error -> resource
                }
            }
    }

    /**
     *  UC-010: Obtiene juegos populares (trending)
     *
     * CASO DE USO:
     * - Sección "Trending Now"
     * - "Lo más vendido de la semana"
     * - Carrusel de juegos destacados
     *
     * NOTA IMPORTANTE:
     * - Actualmente ordenamos por rating
     * - En producción real requeriría:
     *   * Campo "salesCount" en Game
     *   * O llamada a API de estadísticas de ventas
     *   * O analytics de visualizaciones
     *
     * @param limit Número máximo de juegos a devolver (por defecto 10)
     * @return Flow<Resource<List<Game>>>
     */
    fun getPopularGames(limit: Int = 10): Flow<Resource<List<Game>>> {
        return gamesRepository.getAllGames()
            .map { resource ->
                when (resource) {
                    is Resource.Success -> {
                        // Simulación: ordenar por rating
                        // En producción: ordenar por ventas/popularidad
                        val popular = resource.data
                            .sortedByDescending { it.rating }
                            .take(limit) // Tomar solo los primeros N
                        Resource.Success(popular)
                    }
                    is Resource.Loading -> resource
                    is Resource.Error -> resource
                }
            }
    }
}
```


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

// Filtrar juegos baratos
val cheapGames = games.filter { it.price <= 20.0 }

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


**¿Cuándo usar cada uno?**

| Caso | Usar |
|------|------|
| Una sola respuesta | `suspend fun` |
| Stream de datos | `Flow` |
| Datos que cambian | `Flow` |
| Operación única | `suspend fun` |

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


---

### ✅ Resumen de la Fase 2

Se ha completado la **capa de dominio** con:

1. ✅ **Clase GameUseCases** con 10 casos de uso:
2. ✅ **Lógica de negocio** implementada.


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

import android.content.Context
import androidx.lifecycle.ViewModel
import androidx.lifecycle.ViewModelProvider
import androidx.lifecycle.viewModelScope
import com.pmdm.mygamestore.data.repository.GamesRepository
import com.pmdm.mygamestore.data.repository.MockGamesRepositoryImpl
import com.pmdm.mygamestore.data.repository.SessionManager
import com.pmdm.mygamestore.data.repository.SessionManagerImpl
import com.pmdm.mygamestore.domain.model.AppError
import com.pmdm.mygamestore.domain.model.Game
import com.pmdm.mygamestore.domain.model.GameCategory
import com.pmdm.mygamestore.domain.model.DateInterval
import com.pmdm.mygamestore.domain.model.Platform
import com.pmdm.mygamestore.domain.model.Resource
import com.pmdm.mygamestore.domain.usecase.GameUseCases
import kotlinx.coroutines.flow.MutableStateFlow
import kotlinx.coroutines.flow.StateFlow
import kotlinx.coroutines.flow.asStateFlow
import kotlinx.coroutines.flow.catch
import kotlinx.coroutines.flow.update
import kotlinx.coroutines.launch

/**
 *  Estado UI de la pantalla Home
 *
 * PATRÓN: Single Source of Truth
 * - Toda la información de la UI está en un solo objeto
 * - La UI es función del estado: UI = f(state)
 * - Cambio de estado → Recomposición automática
 *
 * Representa TODO lo que la UI necesita para renderizarse.
 *
 * INMUTABILIDAD:
 * - Es data class con val (inmutable)
 * - No se modifica directamente
 * - Se crea nueva instancia con copy()
 *
 * @property games Lista de juegos a mostrar en el grid
 * @property isLoading Indica si hay una operación en progreso
 * @property errorMessage Mensaje de error a mostrar (null si no hay)
 * @property username Nombre del usuario logueado (para TopBar)
 * @property searchQuery Texto actual de búsqueda
 * @property selectedCategory Categoría seleccionada en filtros
 * @property selectedPlatform Plataforma seleccionada en filtros
 * @property selectedInterval Intervalo de fechas seleccionado en filtros
 */
data class HomeUiState(
    val games: List<Game> = emptyList(),
    val isLoading: Boolean = false,
    val errorMessage: String? = null,
    val username: String? = null,
    
    // Filtros activos
    val searchQuery: String = "",
    val selectedCategory: GameCategory = GameCategory.ALL,
    val selectedPlatform: Platform = Platform.ALL,
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
 *  ViewModel para la pantalla Home
 *
 * PATRÓN MVVM:
 * - Model: Game, GameUseCases, GamesRepository
 * - View: HomeScreen (Composable)
 * - ViewModel: HomeViewModel (esta clase)
 *
 * RESPONSABILIDADES:
 * ✅ Gestionar el estado de la UI (HomeUiState)
 * ✅ Coordinar casos de uso (GameUseCases)
 * ✅ Manejar eventos del usuario (búsqueda, filtros, clicks)
 * ✅ Transformar Resource en estado UI
 * ✅ Gestionar corrutinas con viewModelScope
 * ✅ NO tiene referencias a Views (evita memory leaks)
 *
 * MANEJO DE RESOURCE:
 * - Resource.Loading → uiState.isLoading = true
 * - Resource.Success → uiState.games = data
 * - Resource.Error → uiState.errorMessage = error
 *
 * IMPORTANTE - Sin DI por ahora:
 * - gameUseCases se instancia directamente aquí
 * - sessionManager se instancia directamente aquí
 * - Cuando se implemente Koin, se recibirán por constructor
 *
 * @param context Contexto de Android (para SessionManager)
 */
class HomeViewModel(
    context: Context
) : ViewModel() {

    //  Dependencias instanciadas directamente (temporal, antes de Koin)
    // Cuando implementes Koin DI, estas líneas se eliminarán
    // y las dependencias se recibirán por constructor
    private val gamesRepository: GamesRepository = MockGamesRepositoryImpl()
    private val gameUseCases = GameUseCases(gamesRepository)
    private val sessionManager: SessionManager = SessionManagerImpl(context)

    //  Estado privado mutable (solo modificable desde el ViewModel)
    private val _uiState = MutableStateFlow(HomeUiState())
    
    //  Estado público inmutable (expuesto a la UI)
    val uiState: StateFlow<HomeUiState> = _uiState.asStateFlow()

    init {
        //  Cargar datos iniciales al crear el ViewModel
        loadUsername()
        loadGames()
    }

    /**
     *  Carga el nombre del usuario desde SessionManager
     *
     * Se ejecuta en paralelo con loadGames() gracias a viewModelScope.
     * Cada launch crea una corrutina independiente.
     */
    private fun loadUsername() {
        viewModelScope.launch {
            sessionManager.getUsername()
                .catch { exception ->
                    // Manejo de errores en el Flow
                    // No bloqueamos la carga de juegos si falla esto
                    println("Error loading username: ${exception.message}")
                }
                .collect { username ->
                    _uiState.update { it.copy(username = username) }
                }
        }
    }

    /**
     *  Carga los juegos aplicando filtros activos
     *
     * LÓGICA DE PRIORIDAD DE FILTROS:
     * 1. Búsqueda por texto (mayor prioridad)
     * 2. Intervalo de fechas
     * 3. Plataforma
     * 4. Categoría
     * 5. Todos los juegos (sin filtros)
     *
     * MANEJO DE RESOURCE:
     * - Loading → Mostrar spinner
     * - Success → Mostrar juegos
     * - Error → Mostrar mensaje
     */
    fun loadGames() {
        viewModelScope.launch {
            val currentState = _uiState.value
            
            // Determinar qué UseCase llamar según filtros activos
            val gamesFlow = when {
                //  Prioridad 1: Búsqueda por texto
                currentState.searchQuery.isNotBlank() -> {
                    gameUseCases.searchGames(currentState.searchQuery)
                }
                
                //  Prioridad 2: Filtro por intervalo de fechas
                currentState.selectedInterval != DateInterval.ALL_TIME -> {
                    gameUseCases.getGamesInterval(currentState.selectedInterval)
                }
                
                //  Prioridad 3: Filtro por plataforma
                currentState.selectedPlatform != Platform.ALL -> {
                    gameUseCases.getGamesByPlatform(currentState.selectedPlatform)
                }
                
                //  Prioridad 4: Filtro por categoría
                currentState.selectedCategory != GameCategory.ALL -> {
                    gameUseCases.getGamesByCategory(currentState.selectedCategory)
                }
                
                //  Por defecto: Todos los juegos
                else -> {
                    gameUseCases.getAllGames()
                }
            }

            //  Recolectar el Flow y manejar Resource
            gamesFlow.collect { resource ->
                when (resource) {
                    is Resource.Loading -> {
                        // ⏳ Estado Loading: Mostrar spinner
                        _uiState.update { 
                            it.copy(
                                isLoading = true,
                                errorMessage = null
                            )
                        }
                    }
                    
                    is Resource.Success -> {
                        // ✅ Estado Success: Mostrar juegos
                        _uiState.update { 
                            it.copy(
                                games = resource.data,
                                isLoading = false,
                                errorMessage = null
                            )
                        }
                    }
                    
                    is Resource.Error -> {
                        // ❌ Estado Error: Mostrar mensaje
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

    /**
     *  Evento: Usuario escribe en la búsqueda
     *
     * @param query Nuevo texto de búsqueda
     */
    fun onSearchQueryChange(query: String) {
        _uiState.update { it.copy(searchQuery = query) }
        loadGames() // Recargar con nuevo criterio
    }

    /**
     *  Evento: Usuario selecciona una categoría
     *
     * @param category Nueva categoría
     */
    fun onCategorySelected(category: GameCategory) {
        _uiState.update { 
            it.copy(
                selectedCategory = category,
                // Limpiar otros filtros al seleccionar categoría
                searchQuery = "",
                selectedInterval = DateInterval.ALL_TIME,
                selectedPlatform = Platform.ALL
            )
        }
        loadGames()
    }

    /**
     *  Evento: Usuario selecciona una plataforma
     *
     * @param platform Nueva plataforma
     */
    fun onPlatformSelected(platform: Platform) {
        _uiState.update { 
            it.copy(
                selectedPlatform = platform,
                searchQuery = "",
                selectedInterval = DateInterval.ALL_TIME,
                selectedCategory = GameCategory.ALL
            )
        }
        loadGames()
    }

    /**
     *  Evento: Usuario selecciona un intervalo de fechas
     *
     * @param interval Nuevo intervalo
     */
    fun onIntervalSelected(interval: DateInterval) {
        _uiState.update { 
            it.copy(
                selectedInterval = interval,
                searchQuery = "",
                selectedCategory = GameCategory.ALL,
                selectedPlatform = Platform.ALL
            )
        }
        loadGames()
    }

    /**
     *  Evento: Usuario hace pull-to-refresh
     */
    fun refreshGames() {
        loadGames()
    }

    /**
     * ❌ Limpia el mensaje de error después de mostrarlo
     */
    fun clearError() {
        _uiState.update { it.copy(errorMessage = null) }
    }

    /**
     *  Limpia todos los filtros y vuelve a estado inicial
     */
    fun clearAllFilters() {
        _uiState.update {
            it.copy(
                searchQuery = "",
                selectedCategory = GameCategory.ALL,
                selectedPlatform = Platform.ALL,
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
 * IMPORTANTE - TEMPORAL:
 * ✅ Esta factory es TEMPORAL
 * ✅ Solo existe porque HomeViewModel necesita Context
 * ✅ Cuando se implemente Koin DI, esta clase se ELIMINARÁ
 * ✅ En su lugar: viewModel = koinViewModel()
 *
 * MIGRACIÓN A KOIN:
 * ```kotlin
 * // Antes (con Factory)
 * val viewModel: HomeViewModel = viewModel(
 *     factory = HomeViewModelFactory(context)
 * )
 *
 * // Después (con Koin)
 * val viewModel: HomeViewModel = koinViewModel()
 * ```
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

### ✅ Resumen de la Fase 3

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

Esta fase se centra en crear componentes Compose reutilizables, stateless y bien organizados para construir la interfaz de HomeScreen.

---

!!! info "Dependencia de Coil"
     Antes de crear los componentes UI, necesitamos agregar **Coil** para cargar imágenes desde URLs externas.

    *¿Qué es Coil?*

    **Coil** (Coroutine Image Loader) es una librería moderna de carga de imágenes para Android que:

    - ✅ Está optimizada para Kotlin y Coroutines
    - ✅ Es ligera y rápida
    - ✅ Tiene soporte nativo para Jetpack Compose
    - ✅ Cachea imágenes automáticamente (memoria + disco)
    - ✅ Maneja placeholders, error states y transformaciones

    *Agregar Dependencia*

    **Ubicación**: `app/build.gradle.kts`

    ```kotlin
    dependencies {
        // ... otras dependencias existentes

        // Coil para carga de imágenes
        implementation("io.coil-kt:coil-compose:2.5.0")
    }
    ```


    *Sincronizar Proyecto*

    Después de agregar la dependencia:

    1. Click en **"Sync Now"** en la barra superior
    2. O ejecuta: **File → Sync Project with Gradle Files**

    *Uso Básico en Compose*

    Una vez instalada, puedes usar `AsyncImage` en tus composables:

    ```kotlin
    import coil.compose.AsyncImage

    @Composable
    fun GameImage(imageUrl: String) {
        AsyncImage(
            model = imageUrl,
            contentDescription = "Game cover",
            modifier = Modifier.size(200.dp),
            contentScale = ContentScale.Crop,
            placeholder = painterResource(R.drawable.placeholder), // Opcional
            error = painterResource(R.drawable.error_image)        // Opcional
        )
    }
    ```


    *Características de AsyncImage*

    - **Automática**: Descarga, cachea y muestra la imagen
    - **Placeholders**: Muestra imagen temporal mientras carga
    - **Error handling**: Muestra imagen de error si falla
    - **Content scale**: Crop, Fit, FillBounds, etc.
    - **Transformations**: Círculo, bordes redondeados, blur, etc.

    ---

    *✅ Verificación*

    Para verificar que Coil está correctamente instalado:

    ```kotlin
    // En cualquier @Composable
    AsyncImage(
        model = "https://picsum.photos/400/600",
        contentDescription = null
    )
    ```


    Si la imagen se muestra correctamente, **Coil está listo para usar** en los componentes GameCard y GameGrid.

---

### 📁 Paso 4.1: Estructura de Carpetas

Organizar los componentes en la carpeta existente `presentation/ui/componentes`:

```
presentation/ui/componentes/
  ├─ BotonGS.kt              ← Ya existe (reutilizar)
  ├─ TextFieldGS.kt          ← Ya existe (reutilizar para SearchBar)
  ├─ LoadingIndicator.kt     ← Nuevo
  ├─ ErrorMessage.kt         ← Nuevo
  ├─ EmptyState.kt           ← Nuevo
  ├─ CategoryChipsRow.kt     ← Nuevo
  ├─ GameCard.kt             ← Nuevo
  └─ GameGrid.kt             ← Nuevo
```


---

### 🔍 Paso 4.2: SearchBar Component (Reutilizando TextFieldGS)

**Ubicación**: Usar directamente `TextFieldGS` en HomeScreen

No necesitas crear un componente nuevo, usa `TextFieldGS` existente:

```kotlin
// En HomeScreen.kt
TextFieldGS(
    value = searchQuery,
    onValueChange = onSearchQueryChange,
    placeholder = "Search games...",
    modifier = Modifier.fillMaxWidth(),
    singleLine = true
)
```


**Ventajas de reutilizar**:
- ✅ Ya tiene el estilo personalizado del proyecto
- ✅ Colores y forma consistentes con el tema
- ✅ Menos código duplicado

---

### 🏷️ Paso 4.3: CategoryChipsRow Component

**Ubicación**: `presentation/ui/componentes/CategoryChipsRow.kt`

```kotlin
@Composable
fun CategoryChipsRow(
    selectedCategory: GameCategory,
    onCategorySelected: (GameCategory) -> Unit,
    modifier: Modifier = Modifier
) {
    LazyRow(
        modifier = modifier,
        horizontalArrangement = Arrangement.spacedBy(8.dp),
        contentPadding = PaddingValues(horizontal = 16.dp)
    ) {
        items(GameCategory.entries) { category ->
            FilterChip(
                selected = category == selectedCategory,
                onClick = { onCategorySelected(category) },
                label = { Text(category.name) }
            )
        }
    }
}
```


---

### 🎮 Paso 4.4: GameCard Component

**Ubicación**: `presentation/ui/componentes/GameCard.kt`

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
            .clickable(onClick = onClick),
        elevation = CardDefaults.cardElevation(defaultElevation = 4.dp)
    ) {
        Column {
            // Imagen con Coil
            AsyncImage(
                model = game.imageUrl,
                contentDescription = game.title,
                modifier = Modifier
                    .fillMaxWidth()
                    .height(180.dp),
                contentScale = ContentScale.Crop
            )
            
            Column(modifier = Modifier.padding(12.dp)) {
                Text(
                    text = game.title,
                    style = MaterialTheme.typography.titleMedium,
                    maxLines = 2,
                    overflow = TextOverflow.Ellipsis
                )
                
                Spacer(modifier = Modifier.height(4.dp))
                
                Row(
                    modifier = Modifier.fillMaxWidth(),
                    horizontalArrangement = Arrangement.SpaceBetween
                ) {
                    // Rating
                    Row(verticalAlignment = Alignment.CenterVertically) {
                        Icon(
                            Icons.Default.Star,
                            contentDescription = null,
                            tint = Color(0xFFFFD700),
                            modifier = Modifier.size(16.dp)
                        )
                        Text(
                            text = game.rating.toString(),
                            style = MaterialTheme.typography.bodySmall
                        )
                    }
                    
                    // Precio
                    Text(
                        text = "$${game.price}",
                        style = MaterialTheme.typography.titleMedium,
                        fontWeight = FontWeight.Bold
                    )
                }
            }
        }
    }
}
```


---

### 📱 Paso 4.5: GameGrid Component

**Ubicación**: `presentation/ui/componentes/GameGrid.kt`

```kotlin
@Composable
fun GameGrid(
    games: List<Game>,
    onGameClick: (Int) -> Unit,
    modifier: Modifier = Modifier
) {
    LazyVerticalGrid(
        columns = GridCells.Fixed(2),
        modifier = modifier,
        contentPadding = PaddingValues(16.dp),
        horizontalArrangement = Arrangement.spacedBy(12.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp)
    ) {
        items(
            items = games,
            key = { game -> game.id }
        ) { game ->
            GameCard(
                game = game,
                onClick = { onGameClick(game.id) }
            )
        }
    }
}
```


---

### 🔄 Paso 4.6: LoadingIndicator Component

**Ubicación**: `presentation/ui/componentes/LoadingIndicator.kt`

```kotlin
@Composable
fun LoadingIndicator(modifier: Modifier = Modifier) {
    Box(
        modifier = modifier.fillMaxSize(),
        contentAlignment = Alignment.Center
    ) {
        CircularProgressIndicator()
    }
}
```


---

### ⚠️ Paso 4.7: ErrorMessage Component (Reutilizando BotonGS)

**Ubicación**: `presentation/ui/componentes/ErrorMessage.kt`

```kotlin
@Composable
fun ErrorMessage(
    message: String,
    onRetry: () -> Unit,
    modifier: Modifier = Modifier
) {
    Column(
        modifier = modifier
            .fillMaxSize()
            .padding(32.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center
    ) {
        Icon(
            Icons.Default.Error,
            contentDescription = null,
            modifier = Modifier.size(64.dp),
            tint = MaterialTheme.colorScheme.error
        )
        
        Spacer(modifier = Modifier.height(16.dp))
        
        Text(
            text = message,
            style = MaterialTheme.typography.bodyLarge,
            textAlign = TextAlign.Center
        )
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // Reutilizando BotonGS existente
        BotonGS(
            texto = "Retry",
            onClick = onRetry
        )
    }
}
```


---

### 📭 Paso 4.8: EmptyState Component (Reutilizando BotonGS)

**Ubicación**: `presentation/ui/componentes/EmptyState.kt`

```kotlin
@Composable
fun EmptyState(
    message: String = "No games found",
    onClearFilters: (() -> Unit)? = null,
    modifier: Modifier = Modifier
) {
    Column(
        modifier = modifier
            .fillMaxSize()
            .padding(32.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center
    ) {
        Icon(
            Icons.Default.SearchOff,
            contentDescription = null,
            modifier = Modifier.size(64.dp),
            tint = MaterialTheme.colorScheme.onSurfaceVariant
        )
        
        Spacer(modifier = Modifier.height(16.dp))
        
        Text(
            text = message,
            style = MaterialTheme.typography.bodyLarge,
            textAlign = TextAlign.Center
        )
        
        onClearFilters?.let {
            Spacer(modifier = Modifier.height(16.dp))
            // Reutilizando BotonGS existente
            BotonGS(
                texto = "Clear Filters",
                onClick = it
            )
        }
    }
}
```

!!! note "Iconos"
    Se ha utilizado el icono `Icons.Default.SearchOff` que viene dentro de los iconos extendidos de Material Design, que se encuentran en el paquete `androidx.compose.material.icons.extended`.<br>
    Es necesario agregar la dependencia de `androidx.compose.material.icons.extended` en el archivo `build.gradle` del módulo `app`.

    ```gradle
    implementation "androidx.compose.material:material-icons-extended:1.7.6"
    ```
    


---

### ✅ Resumen de la Fase 4

Has creado componentes UI reutilizables aprovechando los existentes:

**Componentes reutilizados del proyecto**:

- ✅ **TextFieldGS** - Para SearchBar (sin crear componente nuevo)
- ✅ **BotonGS** - Para botones Retry y Clear Filters

**Componentes nuevos creados**:

- ✅ **CategoryChipsRow** - Filtros de categorías
- ✅ **GameCard** - Tarjeta individual con Coil
- ✅ **GameGrid** - Grid de 2 columnas
- ✅ **LoadingIndicator** - Spinner centrado
- ✅ **ErrorMessage** - Error con BotonGS
- ✅ **EmptyState** - Estado vacío con BotonGS
