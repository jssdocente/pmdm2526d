# 📚 Guía Práctica: Implementación de HomeScreen con Catálogo de Juegos

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

Una pantalla de catálogo profesional con las siguientes capacidades:

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

Al completar esta guía, habrás aprendido:

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

## 📦 FASE 1: Capa de Datos - Models y Repository {#fase-1-capa-de-datos}

La capa de datos es responsable de **obtener y gestionar los datos** de la aplicación. En esta fase crearemos:

1. Modelos de dominio (Game, GameCategory, Platform, DateInterval)
2. Interfaz del Repository
3. Implementación mock del Repository

### 📝 Paso 1.1: Crear el modelo Game

El modelo `Game` representa un videojuego en nuestro catálogo. Es una **entidad de dominio**, lo que significa que pertenece a la lógica de negocio y es independiente de frameworks.

<u>ubicación</u>:: `app/src/main/java/com/pmdm/mygamestore/domain/model/Game.kt`

```kotlin
package com.pmdm.mygamestore.domain.model

import kotlinx.serialization.Serializable

/**
 * 🎮 Modelo de dominio que representa un juego en el catálogo
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

---

### 📝 Paso 1.2: Crear enums para categorías, plataformas e intervalos

Los **enums** nos permiten definir conjuntos cerrados de valores posibles, proporcionando type-safety y evitando errores.

<u>ubicación</u>:: `app/src/main/java/com/pmdm/mygamestore/domain/model/GameEnums.kt`

```kotlin
package com.pmdm.mygamestore.domain.model

/**
 * 🎯 Categorías principales de juegos
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
 * 🎮 Plataformas de videojuegos
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
 * 📅 Intervalos de fechas para filtrar lanzamientos
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


**💡 ¿Por qué usar enums?**

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
```kotlin
// Cambiar ACTION a ACTION_ADVENTURE actualiza todas las referencias
```


5. **Iterable**: Podemos iterar sobre todos los valores
```kotlin
GameCategory.entries.forEach { category ->
    // Procesar cada categoría
}
```


---

### 📝 Paso 1.3: Crear interfaz del Repository

El **patrón Repository** abstrae el origen de los datos. La UI no sabe (ni debe saber) si los datos vienen de:

- Mock hardcodeado ✅ (lo que haremos ahora)
- API REST 🌐
- Base de datos local 💾
- Caché híbrida 🔄

<u>ubicación</u>:: `app/src/main/java/com/pmdm/mygamestore/data/repository/GamesRepository.kt`

```kotlin
package com.pmdm.mygamestore.data.repository

import com.pmdm.mygamestore.domain.model.Game
import com.pmdm.mygamestore.domain.model.GameCategory
import com.pmdm.mygamestore.domain.model.DateInterval
import com.pmdm.mygamestore.domain.model.Platform
import kotlinx.coroutines.flow.Flow

/**
 * 📋 Interfaz que define las operaciones del repositorio de juegos
 *
 * PATRÓN REPOSITORY:
 * ✅ Abstrae la fuente de datos
 * ✅ Permite cambiar implementación sin afectar el resto del código
 * ✅ Facilita testing con implementaciones mock
 * ✅ Aplica el principio de Inversión de Dependencias (SOLID)
 *
 * BENEFICIOS:
 * - Desacoplamiento: La UI no sabe de dónde vienen los datos
 * - Testing: Fácil crear mocks para tests
 * - Flexibilidad: Cambiar de mock a API sin modificar UseCases
 * - Mantenibilidad: Un solo punto de cambio para el origen de datos
 */
interface GamesRepository {

    /**
     * Obtiene todos los juegos disponibles en el catálogo
     *
     * @return Flow que emite la lista completa de juegos
     *
     * Ejemplo de uso:
     * ```
     * gamesRepository.getAllGames().collect { games ->
     *     println("Total games: ${games.size}")
     * }
     * ```
     */
    fun getAllGames(): Flow<List<Game>>

    /**
     * Filtra juegos por categoría
     *
     * @param category Categoría a filtrar (ACTION, RPG, etc.)
     * @return Flow con juegos de la categoría especificada
     *
     * Casos de uso:
     * - Usuario selecciona "RPG" en filtros
     * - Sección "Juegos de acción"
     *
     * Nota: Si category es ALL, devuelve todos los juegos
     */
    fun getGamesByCategory(category: GameCategory): Flow<List<Game>>

    /**
     * Filtra juegos por intervalo de fecha de lanzamiento
     *
     * @param interval Intervalo de tiempo (LAST_WEEK, LAST_30_DAYS, etc.)
     * @return Flow con juegos lanzados en el intervalo
     *
     * Casos de uso:
     * - Sección "Novedades de la semana"
     * - "Lanzamientos del mes"
     * - "Próximos lanzamientos"
     */
    fun getGamesByInterval(interval: DateInterval): Flow<List<Game>>

    /**
     * Filtra juegos por plataforma
     *
     * @param platform Plataforma deseada (PC, PLAYSTATION, etc.)
     * @return Flow con juegos disponibles en la plataforma
     *
     * Caso de uso:
     * - Usuario con PlayStation quiere ver solo juegos de su plataforma
     */
    fun getGamesByPlatform(platform: Platform): Flow<List<Game>>

    /**
     * Filtra juegos por géneros
     *
     * @param genres Lista de géneros a buscar (ej: ["RPG", "Open World"])
     * @return Flow con juegos que contengan al menos uno de los géneros
     *
     * Caso de uso:
     * - Búsqueda multi-género: "RPG" + "Open World"
     *
     * Nota: El filtro es inclusivo (OR), no exclusivo (AND)
     */
    fun getGamesByGenres(genres: List<String>): Flow<List<Game>>

    /**
     * Busca juegos por texto en título o descripción
     *
     * @param query Texto a buscar (case-insensitive)
     * @return Flow con juegos que coincidan con la búsqueda
     *
     * Búsqueda en:
     * - Título del juego
     * - Descripción
     *
     * Ejemplo:
     * ```
     * searchGames("witcher") // Encuentra "The Witcher 3"
     * ```
     */
    fun searchGames(query: String): Flow<List<Game>>

    /**
     * Obtiene un juego específico por su ID
     *
     * @param id Identificador del juego
     * @return Juego encontrado o null si no existe
     *
     * Casos de uso:
     * - Pantalla de detalles
     * - Deep linking a un juego específico
     * - Compartir enlace de juego
     *
     * Nota: Es suspend porque puede requerir operaciones I/O
     */
    suspend fun getGameById(id: Int): Game?
}
```

---

### 📝 Paso 1.4: Implementar el Repository con datos mock

Ahora crearemos la implementación concreta del repository usando **datos hardcodeados** (mock). Más adelante, podremos reemplazar esto con una implementación que llame a una API real.

<u>ubicación</u>:: `app/src/main/java/com/pmdm/mygamestore/data/repository/GamesRepositoryImpl.kt`

```kotlin
package com.pmdm.mygamestore.data.repository

import com.pmdm.mygamestore.domain.model.Game
import com.pmdm.mygamestore.domain.model.GameCategory
import com.pmdm.mygamestore.domain.model.DateInterval
import com.pmdm.mygamestore.domain.model.Platform
import kotlinx.coroutines.delay
import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.flow.flow
import java.time.LocalDate
import java.time.format.DateTimeFormatter

/**
 * 🔧 Implementación local del repositorio de juegos
 *
 * IMPORTANTE:
 * - Esta implementación usa datos MOCK (hardcodeados)
 * - Simula delays de red para hacer realista la experiencia
 * - En producción, esto se reemplazaría por llamadas a API
 *
 * ESCENARIOS REALES:
 * 🌐 API REST: Usar Retrofit para llamar a backend
 * 💾 Base de datos: Usar Room para persistencia local
 * ☁️ Firebase: Usar Firestore para datos en la nube
 * 🔄 Híbrido: Combinar caché local + API (patrón caché-first)
 */
class GamesRepositoryImpl : GamesRepository {

    /**
     * 🎮 Base de datos simulada de juegos
     *
     * En una app real, esto vendría de:
     * - API: https://api.rawg.io/api/games
     * - Database: Room con @Entity Game
     * - Firebase: Firestore collection "games"
     */
    private val mockGames = listOf(
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

    /**
     * ⏱️ Simula delay de red para hacer realista la experiencia
     *
     * En una app real, esto sería:
     * - El tiempo de respuesta del servidor (100-2000ms)
     * - El tiempo de lectura de base de datos (10-100ms)
     *
     * Beneficios de simular delay:
     * ✅ Testear estados de loading
     * ✅ Ver cómo se comporta la UI durante cargas
     * ✅ Simular condiciones de red lenta
     */
    private suspend fun simulateNetworkDelay() {
        delay(800) // 800ms de delay simulado
    }

    override fun getAllGames(): Flow<List<Game>> = flow {
        simulateNetworkDelay()
        emit(mockGames)
    }

    override fun getGamesByCategory(category: GameCategory): Flow<List<Game>> = flow {
        simulateNetworkDelay()
        
        val filtered = if (category == GameCategory.ALL) {
            mockGames
        } else {
            mockGames.filter { it.category == category }
        }
        
        emit(filtered)
    }

    override fun getGamesByInterval(interval: DateInterval): Flow<List<Game>> = flow {
        simulateNetworkDelay()
        
        val now = LocalDate.now()
        val filtered = when (interval) {
            DateInterval.ALL_TIME -> mockGames
            
            DateInterval.LAST_WEEK -> mockGames.filter {
                val gameDate = LocalDate.parse(it.releaseDate, DateTimeFormatter.ISO_DATE)
                gameDate.isAfter(now.minusWeeks(1))
            }
            
            DateInterval.LAST_30_DAYS -> mockGames.filter {
                val gameDate = LocalDate.parse(it.releaseDate, DateTimeFormatter.ISO_DATE)
                gameDate.isAfter(now.minusDays(30))
            }
            
            DateInterval.LAST_90_DAYS -> mockGames.filter {
                val gameDate = LocalDate.parse(it.releaseDate, DateTimeFormatter.ISO_DATE)
                gameDate.isAfter(now.minusDays(90))
            }
        }
        
        emit(filtered)
    }

    override fun getGamesByPlatform(platform: Platform): Flow<List<Game>> = flow {
        simulateNetworkDelay()
        
        val filtered = if (platform == Platform.ALL) {
            mockGames
        } else {
            mockGames.filter { it.platform == platform }
        }
        
        emit(filtered)
    }

    override fun getGamesByGenres(genres: List<String>): Flow<List<Game>> = flow {
        simulateNetworkDelay()
        
        val filtered = mockGames.filter { game ->
            // Retorna true si el juego tiene al menos uno de los géneros buscados
            game.genres.any { genre -> 
                genres.any { it.equals(genre, ignoreCase = true) }
            }
        }
        
        emit(filtered)
    }

    override fun searchGames(query: String): Flow<List<Game>> = flow {
        simulateNetworkDelay()
        
        val filtered = mockGames.filter { game ->
            game.title.contains(query, ignoreCase = true) ||
            game.description.contains(query, ignoreCase = true)
        }
        
        emit(filtered)
    }

    override suspend fun getGameById(id: Int): Game? {
        simulateNetworkDelay()
        return mockGames.find { it.id == id }
    }
}
```


**📚 Conceptos importantes del Repository:**

**1. Flow builder (flow { })**

```kotlin
fun getAllGames(): Flow<List<Game>> = flow {
    // Código asíncrono
    delay(1000)
    
    // Emitir valores
    emit(mockGames)
}
```


- `flow { }` crea un Flow **cold** (frío)
- Solo se ejecuta cuando alguien hace `collect()`
- Permite múltiples collectors
- Se cancela automáticamente

**2. emit() vs return**

```kotlin
// ❌ No puedes usar return en Flow
fun getData(): Flow<String> = flow {
    return "Data" // ❌ Error de compilación
}

// ✅ Usa emit
fun getData(): Flow<String> = flow {
    emit("Data") // ✅ Correcto
}
```


**3. filter() y operadores de colecciones**

```kotlin
// filter: Filtra elementos que cumplen condición
val rpgGames = mockGames.filter { it.category == GameCategory.RPG }

// find: Encuentra el primer elemento que cumple condición
val game = mockGames.find { it.id == 5 }

// any: Verifica si al menos un elemento cumple condición
val hasRpg = mockGames.any { it.category == GameCategory.RPG }

// map: Transforma cada elemento
val titles = mockGames.map { it.title }
```


**4. LocalDate para manejo de fechas**

```kotlin
import java.time.LocalDate

val now = LocalDate.now() // Fecha actual
val lastWeek = now.minusWeeks(1) // Hace 1 semana
val gameDate = LocalDate.parse("2024-01-15") // Parsear fecha
val isRecent = gameDate.isAfter(lastWeek) // Comparar
```


**5. Simulación de API real**

En producción, esto cambiaría a:

```kotlin
// Con Retrofit
@GET("games")
suspend fun getAllGames(): Response<List<GameDto>>

// Implementación
override fun getAllGames(): Flow<List<Game>> = flow {
    val response = api.getAllGames()
    if (response.isSuccessful) {
        val games = response.body()?.map { it.toDomain() }
        emit(games ?: emptyList())
    } else {
        throw Exception("Error: ${response.code()}")
    }
}
```

---

## 🎯 FASE 2: Capa de Dominio - UseCases {#fase-2-capa-de-dominio}

La capa de dominio contiene la **lógica de negocio** de la aplicación. Los UseCases son el corazón de esta capa y representan las acciones que un usuario puede realizar.

### ¿Qué son los UseCases?

Los **UseCases** (Casos de Uso) son clases que:

- ✅ Encapsulan lógica de negocio específica
- ✅ Orquestan llamadas a repositories
- ✅ Transforman y procesan datos según las reglas de negocio
- ✅ Son independientes del framework (Android, iOS, Web)

### Arquitectura de nuestros UseCases

En este proyecto, los UseCases están **agrupados por funcionalidad**:

```
📁 domain/usecase/
   📄 GameUseCases.kt      ← Todos los casos de uso de Game
   📄 LibraryUseCases.kt   ← (Futuro) Casos de uso de Library
   📄 UserUseCases.kt      ← (Futuro) Casos de uso de User
```


### 📝 Paso 2.1: Crear clase GameUseCases

Esta clase agrupa **todos los casos de uso relacionados con juegos**. Cada método representa una acción específica que el usuario puede realizar.

**Ubicación**: `app/src/main/java/com/pmdm/mygamestore/domain/usecase/GameUseCases.kt`

```kotlin
package com.pmdm.mygamestore.domain.usecase

import com.pmdm.mygamestore.data.repository.GamesRepository
import com.pmdm.mygamestore.domain.model.Game
import com.pmdm.mygamestore.domain.model.GameCategory
import com.pmdm.mygamestore.domain.model.DateInterval
import com.pmdm.mygamestore.domain.model.Platform
import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.flow.map

/**
 * 🎯 Casos de uso agrupados para operaciones con juegos
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
 * ✅ Preparado para inyección de dependencias con Koin
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
     * 📋 UC-001: Obtiene todos los juegos del catálogo
     *
     * CASO DE USO:
     * - Usuario abre la app
     * - Usuario limpia todos los filtros
     * - Vista por defecto del catálogo
     *
     * LÓGICA DE NEGOCIO:
     * - Obtiene todos los juegos sin filtrar
     * - En el futuro podría agregar ordenamiento por defecto
     *
     * @return Flow con la lista completa de juegos
     *
     * Ejemplo de uso:
     * ```kotlin
     * gameUseCases.getAllGames().collect { games ->
     *     println("Total de juegos: ${games.size}")
     * }
     * ```
     */
    fun getAllGames(): Flow<List<Game>> {
        return gamesRepository.getAllGames()
    }

    /**
     * 🎮 UC-002: Filtra juegos por categoría
     *
     * CASO DE USO:
     * - Usuario hace click en chip "RPG"
     * - Usuario navega a sección "Juegos de Acción"
     *
     * LÓGICA DE NEGOCIO:
     * - Si category es ALL, devuelve todos los juegos
     * - Si no, filtra por categoría específica
     * - Ordena por rating (mejor valorados primero)
     *
     * ¿Por qué ordenar por rating?
     * - Mejora la experiencia: usuarios ven primero los mejores juegos
     * - Aumenta probabilidad de compra
     * - Es una práctica común en tiendas (Steam, Epic, PlayStation Store)
     *
     * @param category Categoría a filtrar (ACTION, RPG, etc.)
     * @return Flow con juegos filtrados y ordenados por rating
     */
    fun getGamesByCategory(category: GameCategory): Flow<List<Game>> {
        return gamesRepository.getGamesByCategory(category)
            .map { games ->
                // Lógica adicional: ordenar por rating descendente
                games.sortedByDescending { it.rating }
            }
    }

    /**
     * 📅 UC-003: Filtra juegos por intervalo de fecha de lanzamiento
     *
     * CASOS DE USO:
     * - Sección "Novedades de la semana"
     * - Sección "Lanzamientos del mes"
     * - Filtro "Últimos 90 días"
     *
     * LÓGICA DE NEGOCIO:
     * - Filtra según el intervalo seleccionado
     * - Ordena por fecha de lanzamiento (más recientes primero)
     *
     * REGLAS DE NEGOCIO:
     * - LAST_WEEK: Juegos lanzados en los últimos 7 días
     * - LAST_30_DAYS: Juegos lanzados en el último mes
     * - LAST_90_DAYS: Juegos lanzados en los últimos 3 meses
     * - ALL_TIME: Todos los juegos sin filtro de fecha
     *
     * @param interval Intervalo de tiempo
     * @return Flow con juegos lanzados en el intervalo, ordenados por fecha
     */
    fun getGamesInterval(interval: DateInterval): Flow<List<Game>> {
        return gamesRepository.getGamesByInterval(interval)
            .map { games ->
                // Ordenar por fecha de lanzamiento (más recientes primero)
                games.sortedByDescending { it.releaseDate }
            }
    }

    /**
     * 🎮 UC-004: Filtra juegos por plataforma
     *
     * CASO DE USO:
     * - Usuario tiene PlayStation y solo quiere ver juegos compatibles
     * - Filtro de plataforma en la UI
     *
     * LÓGICA DE NEGOCIO:
     * - Si platform es ALL, devuelve todos
     * - Si no, filtra por plataforma específica
     *
     * MEJORA FUTURA:
     * - Podría ordenar por popularidad en esa plataforma
     * - Mostrar exclusivos primero
     *
     * @param platform Plataforma deseada (PC, PLAYSTATION, etc.)
     * @return Flow con juegos de la plataforma
     */
    fun getGamesByPlatform(platform: Platform): Flow<List<Game>> {
        return gamesRepository.getGamesByPlatform(platform)
    }

    /**
     * 🏷️ UC-005: Filtra juegos por géneros
     *
     * CASO DE USO:
     * - Usuario busca juegos con etiquetas específicas
     * - Búsqueda multi-género: "RPG" + "Open World"
     *
     * LÓGICA DE NEGOCIO:
     * - Filtro inclusivo (OR): El juego debe tener AL MENOS uno de los géneros
     * - No es exclusivo (AND): No requiere tener TODOS los géneros
     *
     * Ejemplo:
     * - Buscar ["RPG", "Fantasy"]
     * - "The Witcher" (RPG + Fantasy) → ✅ Incluido
     * - "Elden Ring" (RPG + Dark Fantasy) → ✅ Incluido
     * - "FIFA" (Sports) → ❌ Excluido
     *
     * @param genres Lista de géneros a buscar
     * @return Flow con juegos que contengan al menos un género
     */
    fun getGamesByGenres(genres: List<String>): Flow<List<Game>> {
        return gamesRepository.getGamesByGenres(genres)
    }

    /**
     * 🔍 UC-006: Busca juegos por texto
     *
     * CASO DE USO:
     * - Usuario escribe "witcher" en la barra de búsqueda
     * - Búsqueda en tiempo real mientras escribe
     *
     * LÓGICA DE NEGOCIO:
     * - Si query está vacío → devuelve todos los juegos
     * - Si no → busca en título y descripción
     * - Ordena por relevancia (coincidencias en título primero)
     *
     * ORDENAMIENTO POR RELEVANCIA:
     * 1. Coincidencia en título (prioridad 2)
     * 2. Coincidencia en descripción (prioridad 1)
     * 3. Sin coincidencia (prioridad 0)
     *
     * ¿Por qué este orden?
     * - El título es más relevante que la descripción
     * - Mejora la experiencia del usuario
     * - Similar a buscadores como Google
     *
     * @param query Texto a buscar (case-insensitive)
     * @return Flow con resultados ordenados por relevancia
     *
     * Ejemplo:
     * ```kotlin
     * searchGames("god").collect { games ->
     *     // "God of War" aparecerá primero (coincidencia en título)
     *     // Juegos con "god" en descripción aparecerán después
     * }
     * ```
     */
    fun searchGames(query: String): Flow<List<Game>> {
        // Si la búsqueda está vacía, devolver todos
        if (query.isBlank()) {
            return gamesRepository.getAllGames()
        }
        
        return gamesRepository.searchGames(query)
            .map { games ->
                // Ordenar por relevancia:
                // - Primero los que coincidan en título
                // - Luego los que coincidan en descripción
                games.sortedByDescending { game ->
                    when {
                        game.title.contains(query, ignoreCase = true) -> 2
                        game.description.contains(query, ignoreCase = true) -> 1
                        else -> 0
                    }
                }
            }
    }

    /**
     * 🎯 UC-007: Obtiene un juego específico por ID
     *
     * CASOS DE USO:
     * - Usuario hace click en un juego → navega a DetailScreen
     * - Deep linking: abrir app directamente en un juego
     * - Compartir enlace de juego
     * - Notificación push sobre un juego específico
     *
     * LÓGICA DE NEGOCIO:
     * - Busca el juego por ID único
     * - Devuelve null si no existe
     *
     * Es suspend porque:
     * - Puede requerir operaciones I/O (DB, API)
     * - No es un Flow porque solo devuelve un valor
     *
     * @param id Identificador único del juego
     * @return Juego encontrado o null si no existe
     *
     * Ejemplo:
     * ```kotlin
     * viewModelScope.launch {
     *     val game = gameUseCases.getGameById(5)
     *     if (game != null) {
     *         // Mostrar detalles
     *     } else {
     *         // Mostrar error "Juego no encontrado"
     *     }
     * }
     * ```
     */
    suspend fun getGameById(id: Int): Game? {
        return gamesRepository.getGameById(id)
    }

    /**
     * ⭐ UC-008: Obtiene juegos mejor valorados
     *
     * CASO DE USO:
     * - Sección "Top Rated" en la home
     * - Sección "Mejor valorados de la semana"
     * - Recomendaciones de alta calidad
     *
     * LÓGICA DE NEGOCIO:
     * - Filtra juegos con rating >= minRating (por defecto 4.5)
     * - Ordena por rating descendente
     *
     * ¿Por qué rating 4.5?
     * - Es el estándar de la industria para "excelente"
     * - Steam usa 4.5/5 estrellas para "Overwhelmingly Positive"
     * - PlayStation Store destaca juegos con 4.5+
     *
     * @param minRating Rating mínimo (por defecto 4.5 de 5.0)
     * @return Flow con juegos altamente valorados
     */
    fun getTopRatedGames(minRating: Double = 4.5): Flow<List<Game>> {
        return gamesRepository.getAllGames()
            .map { games ->
                games.filter { it.rating >= minRating }
                    .sortedByDescending { it.rating }
            }
    }

    /**
     * 💰 UC-009: Filtra juegos por rango de precio
     *
     * CASO DE USO:
     * - Usuario busca juegos baratos (< $20)
     * - Sección "Juegos en oferta"
     * - Filtro de presupuesto
     *
     * LÓGICA DE NEGOCIO:
     * - Filtra juegos con price <= maxPrice
     * - Ordena por precio ascendente (más baratos primero)
     *
     * MEJORA FUTURA:
     * - Agregar minPrice para rangos (ej: $20-$40)
     * - Calcular descuentos
     * - Destacar ofertas limitadas
     *
     * @param maxPrice Precio máximo en dólares
     * @return Flow con juegos dentro del presupuesto
     *
     * Ejemplo:
     * ```kotlin
     * // Juegos de menos de $30
     * gameUseCases.getGamesByPriceRange(30.0).collect { games ->
     *     println("Encontrados ${games.size} juegos económicos")
     * }
     * ```
     */
    fun getGamesByPriceRange(maxPrice: Double): Flow<List<Game>> {
        return gamesRepository.getAllGames()
            .map { games ->
                games.filter { it.price <= maxPrice }
                    .sortedBy { it.price } // Más baratos primero
            }
    }

    /**
     * 🔥 UC-010: Obtiene juegos populares (más vendidos)
     *
     * CASO DE USO:
     * - Sección "Trending Now"
     * - "Lo más vendido de la semana"
     *
     * NOTA: Actualmente ordenamos por rating
     * En producción real:
     * - Requeriría campo "salesCount" en Game
     * - O llamada a API de estadísticas de ventas
     *
     * @param limit Número máximo de juegos a devolver
     * @return Flow con los juegos más populares
     */
    fun getPopularGames(limit: Int = 10): Flow<List<Game>> {
        return gamesRepository.getAllGames()
            .map { games ->
                // Simulación: ordenar por rating
                // En producción: ordenar por ventas
                games.sortedByDescending { it.rating }
                    .take(limit) // Tomar solo los primeros N
            }
    }
}
```


---

### 📚 Conceptos clave de los UseCases

#### 1. **Flow.map { } para transformar datos**

```kotlin
fun getTopRatedGames(): Flow<List<Game>> {
    return repository.getAllGames()
        .map { games ->
            // Transformar la lista de juegos
            games.filter { it.rating >= 4.5 }
                 .sortedByDescending { it.rating }
        }
}
```


**¿Qué hace map?**

- Transforma cada emisión del Flow
- No modifica el Flow original
- Devuelve un nuevo Flow transformado

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

// Filtrar por múltiples condiciones
val filtered = games.filter { 
    it.rating >= 4.0 && it.price <= 40.0 
}
```


#### 4. **take() para limitar resultados**

```kotlin
// Tomar los primeros 5 elementos
val top5 = games.take(5)

// Tomar los últimos 3 elementos
val last3 = games.takeLast(3)

// Tomar mientras se cumpla condición
val whileExpensive = games.takeWhile { it.price > 50.0 }
```

---

### 🔄 Flujo completo de un caso de uso

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
5. GamesRepositoryImpl → filtra mockGames
   │                     → emit(filteredGames)
   ▼
6. Flow regresa por las capas:
   Repository → UseCase → ViewModel → UI
   │
   ▼
7. HomeScreen se recompone con nuevos datos
```


---

### ✅ Resumen de la Fase 2

Has completado la **capa de dominio** con:

1. ✅ **Clase GameUseCases** con 10 casos de uso:
   - UC-001: getAllGames
   - UC-002: getGamesByCategory (con ordenamiento)
   - UC-003: getGamesInterval (con ordenamiento)
   - UC-004: getGamesByPlatform
   - UC-005: getGamesByGenres
   - UC-006: searchGames (con relevancia)
   - UC-007: getGameById
   - UC-008: getTopRatedGames
   - UC-009: getGamesByPriceRange
   - UC-010: getPopularGames

2. ✅ **Lógica de negocio** implementada:
   - Ordenamiento por rating
   - Ordenamiento por relevancia en búsqueda
   - Filtrado por rango de precio
   - Limitación de resultados

3. ✅ **Preparado para DI**: La clase recibe repository por constructor

**Próximo paso**: Crear el ViewModel que usará estos UseCases para gestionar el estado de la UI.

