# **14. Sistema de Logging y Debugging**

En esta parte implementaremos un **sistema de logging profesional** usando **Timber**, que nos permitirá depurar la aplicación de forma eficiente y mantener logs apropiados tanto en desarrollo como en producción.

!!! tip "Repositorio de la Aplicación"
    El código fuente de la aplicación se encuentra en el repositorio de GitHub: [MyGameStore](https://github.com/jssdocente/MyGameStore)

#### Resumen

1. ¿Qué es el sistema de logging?
2. Logging en Desarrollo vs Producción
3. Sistema nativo de Android: Log y LogCat
4. ¿Qué registrar y qué no registrar?
5. Timber: La mejor opción para Android

---

## **1. ¿Qué es el sistema de logging?**

El **logging** es el proceso de registrar eventos, acciones y errores que ocurren en la aplicación durante su ejecución.

### 1.1. Propósito

- **Debugging**: Identificar y solucionar problemas durante el desarrollo
- **Monitoreo**: Rastrear el comportamiento de la app en producción
- **Auditoría**: Registrar acciones importantes del usuario o del sistema
- **Análisis**: Entender el flujo de la aplicación y detectar cuellos de botella

### 1.2. Tipos de información que se registra

- 🔵 **Información general** (INFO): Flujo normal de la app
- 🟡 **Advertencias** (WARN): Situaciones anormales pero recuperables
- 🔴 **Errores** (ERROR): Fallos que afectan funcionalidad
- 🟣 **Debug** (DEBUG): Información técnica para desarrollo
- ⚪ **Verbose** (VERBOSE): Detalles muy específicos

### 1.3. Ejemplo visual

```kotlin
// Usuario hace login
Timber.d("🔐 Intentando login para usuario: admin")
Timber.i("✅ Login exitoso")
Timber.e("❌ Login fallido: credenciales inválidas")
```


**LogCat mostrará:**
```
D/LoginViewModel: 🔐 Intentando login para usuario: admin
I/AuthRepository: ✅ Login exitoso
```


---

## **2. Logging en Desarrollo vs Producción**

El logging debe comportarse de forma **diferente** según el entorno de ejecución.

### 2.1. Modo Desarrollo (DEBUG)

**Objetivo:** Máxima visibilidad para detectar problemas.

```kotlin
if (BuildConfig.DEBUG) {
    Timber.plant(Timber.DebugTree())
    Timber.d("🎮 MyGameStore iniciada en modo DEBUG")
}
```


**Características:**

- ✅ Todos los niveles de log visibles (VERBOSE, DEBUG, INFO, WARN, ERROR)
- ✅ Logs detallados con contexto completo
- ✅ Stack traces completas
- ✅ Información de clase, método y línea

### 2.2. Modo Producción (RELEASE)

**Objetivo:** Mínimo impacto en rendimiento, solo errores críticos.

```kotlin
if (!BuildConfig.DEBUG) {
    Timber.plant(CrashReportingTree())
    Timber.i("🎮 MyGameStore iniciada en modo RELEASE")
}
```


**Características:**

- ❌ Sin logs DEBUG y VERBOSE
- ✅ Solo WARN y ERROR
- ✅ Envío a servicios de monitoreo (Firebase Crashlytics, Sentry)
- ✅ Sin información sensible

### 2.3. Implementación en Application

**`MyGameStoreApp.kt`**
```kotlin
class MyGameStoreApp : Application() {
    override fun onCreate() {
        super.onCreate()
        
        // Configurar Timber según el entorno
        if (BuildConfig.DEBUG) {
            Timber.plant(Timber.DebugTree())
            Timber.d("🎮 App iniciada en DEBUG")
        } else {
            Timber.plant(CrashReportingTree())
            Timber.i("🎮 App iniciada en RELEASE")
        }
    }
}

/**
 * Árbol personalizado para producción
 * Solo registra WARN y ERROR
 */
class CrashReportingTree : Timber.Tree() {
    override fun log(priority: Int, tag: String?, message: String, t: Throwable?) {
        if (priority == Log.VERBOSE || priority == Log.DEBUG) {
            return // Ignorar en producción
        }
        
        // Enviar a servicio de monitoreo
        // FirebaseCrashlytics.getInstance().log(message)
        // t?.let { FirebaseCrashlytics.getInstance().recordException(it) }
    }
}
```


### 2.4. Habilitar BuildConfig

**⚠️ Importante:** Desde Android Gradle Plugin 8.0+, `BuildConfig` está deshabilitado por defecto.

**`app/build.gradle.kts`**
```kotlin
android {
    buildFeatures {
        buildConfig = true  // 🔥 Necesario para usar BuildConfig.DEBUG
    }
}
```


---

## **3. Sistema nativo de Android: Log y LogCat**

Android proporciona la clase `Log` para registrar información durante la ejecución.

### 3.1. API de Log

```kotlin
import android.util.Log

Log.v(TAG, "Mensaje verbose")   // VERBOSE: Detalles muy específicos
Log.d(TAG, "Mensaje debug")     // DEBUG: Información de debugging
Log.i(TAG, "Mensaje info")      // INFO: Eventos importantes
Log.w(TAG, "Mensaje warning")   // WARN: Advertencias
Log.e(TAG, "Mensaje error")     // ERROR: Errores
Log.e(TAG, "Error", exception)  // ERROR con excepción
```


### 3.2. Concepto de TAG

El **TAG** identifica la fuente del log. Por convención, se usa el nombre de la clase:

```kotlin
class LoginViewModel {
    companion object {
        private const val TAG = "LoginViewModel"
    }
    
    fun login() {
        Log.d(TAG, "Iniciando login")
    }
}
```


**LogCat mostrará:**
```
D/LoginViewModel: Iniciando login
  ↑ TAG automático
```


### 3.3. LogCat: Visualización de logs

**LogCat** es la herramienta de Android Studio para ver logs en tiempo real.

**Ubicación:** `View > Tool Windows > Logcat` (o `Alt+6`)

**Filtros útiles:**
```
package:com.pmdm.mygamestore    # Solo tu app
LoginViewModel                   # Por TAG
level:error                      # Solo errores
🔐                               # Por emoji
```


### 3.4. Problemas de la API nativa

❌ **TAG manual**: Repetir `TAG` en cada log  
❌ **Sin eliminación automática**: Logs permanecen en producción  
❌ **Verboso**: Código repetitivo  
❌ **Sin formato**: Difícil de leer  

```kotlin
// ❌ Log nativo: Repetitivo y propenso a errores
class LoginViewModel {
    companion object {
        private const val TAG = "LoginViewModel"
    }
    
    fun login() {
        Log.d(TAG, "Iniciando login")
        Log.i(TAG, "Login exitoso")
        Log.e(TAG, "Login fallido", exception)
    }
}
```


---

## **4. ¿Qué registrar y qué no registrar?**

### 4.1. ✅ QUÉ REGISTRAR

**Inicio y fin de operaciones importantes**
```kotlin
Timber.d("🔐 Iniciando login")
Timber.i("✅ Login exitoso")
```


**Errores y excepciones**
```kotlin
Timber.e(exception, "💥 Error al guardar en BD")
```


**Cambios de estado**
```kotlin
Timber.d("⏳ Cargando juegos...")
Timber.i("✅ 150 juegos cargados")
```


**Eventos críticos del usuario**
```kotlin
Timber.i("⭐ Usuario marcó como favorito")
```


**Validaciones fallidas**
```kotlin
Timber.w("⚠️ Email inválido")
```


### 4.2. ❌ QUÉ NO REGISTRAR

**Datos sensibles**
```kotlin
// ❌ NUNCA
Log.d(TAG, "Password: $password")
Log.d(TAG, "Token: $authToken")

// ✅ Ofuscar
Timber.d("🔒 Password: ${password.length} caracteres")
Timber.d("🔑 Token recibido: ${token.take(8)}***")
```


**Información personal (GDPR/LOPD)**
```kotlin
// ❌ NUNCA
Log.d(TAG, "Email: $email")
Log.d(TAG, "DNI: $dni")

// ✅ Ofuscar
Timber.d("📧 Email: ${email.take(3)}***")
```


**Logs en bucles intensivos**
```kotlin
// ❌ Contamina LogCat
list.forEach {
    Log.d(TAG, "Procesando item: $it")
}

// ✅ Resumen
Timber.d("📦 Procesando ${list.size} items")
```


**Información redundante**
```kotlin
// ❌ Ruido innecesario
fun getAllGames(): List<Game> {
    Log.d(TAG, "Entrando en getAllGames")  // Redundante
    return repository.getAllGames()
}
```


### 4.3. Ejemplo práctico

```kotlin
// Repository
override suspend fun login(username: String, password: String): LoginResult {
    Timber.d("🔐 Iniciando login para usuario: $username")
    
    return try {
        delay(1500)
        
        if (validUsers[username] == password) {
            Timber.i("✅ Credenciales válidas")
            db.userDao().insertUser(/* ... */)
            Timber.d("💾 Usuario persistido en Room")
            LoginResult.Success(username)
        } else {
            Timber.w("❌ Credenciales inválidas")
            LoginResult.Error("Invalid credentials")
        }
    } catch (e: Exception) {
        Timber.e(e, "💥 Excepción durante login")
        LoginResult.Error("Database error: ${e.message}")
    }
}
```


---

## **5. Timber: La mejor opción para Android**

### 5.1. ¿Qué es Timber?

**Timber** es una librería de logging creada por Jake Wharton (Square) que simplifica y mejora la API nativa de Log.

**Ventajas:**

- ✅ TAG automático (nombre de clase)
- ✅ Sin logs en producción (configuración)
- ✅ Sintaxis simple
- ✅ Extensible (árboles personalizados)
- ✅ Compatible con Crashlytics

### 5.2. Agregar Timber al proyecto

**`app/build.gradle.kts`**
```kotlin
dependencies {
    implementation("com.jakewharton.timber:timber:5.0.1")
}
```


### 5.3. Configuración

**`MyGameStoreApp.kt`**
```kotlin
class MyGameStoreApp : Application() {
    override fun onCreate() {
        super.onCreate()
        
        if (BuildConfig.DEBUG) {
            Timber.plant(Timber.DebugTree())
        } else {
            Timber.plant(CrashReportingTree())
        }
    }
}
```


### 5.4. Uso básico

```kotlin
class LoginViewModel(...) : ViewModel() {
    
    fun login() {
        Timber.d("🔐 Intentando login")        // DEBUG
        Timber.i("✅ Login exitoso")           // INFO
        Timber.w("⚠️ Token próximo a expirar") // WARN
        Timber.e("❌ Error de red")            // ERROR
        Timber.e(exception, "💥 Excepción")    // ERROR con excepción
        Timber.v("📝 Dato: $value")            // VERBOSE
    }
}
```


**LogCat mostrará:**
```
D/LoginViewModel: 🔐 Intentando login
                  ↑ TAG automático
```


### 5.5. Comparación: Log vs Timber

```kotlin
// ❌ Log nativo
class LoginViewModel {
    companion object {
        private const val TAG = "LoginViewModel"
    }
    
    fun login() {
        Log.d(TAG, "Iniciando login")
    }
}

// ✅ Timber
class LoginViewModel {
    fun login() {
        Timber.d("Iniciando login")  // TAG automático
    }
}
```


### 5.6. Uso de emojis para escaneo visual

```kotlin
// Autenticación
Timber.d("🔐 Iniciando login")
Timber.d("🔑 Token recibido")

// Base de datos
Timber.d("💾 Guardando en Room")
Timber.d("🗑️ Eliminando de biblioteca")

// Operaciones exitosas/fallidas
Timber.i("✅ Operación exitosa")
Timber.e("❌ Operación fallida")
Timber.w("⚠️ Advertencia")

// Procesos
Timber.d("⏳ Cargando datos...")
Timber.d("🔄 Sincronizando...")
```


### 5.7. Eliminación automática en producción

**`proguard-rules.pro`**
```
# Eliminar todos los logs en release
-assumenosideeffects class timber.log.Timber {
    public static *** v(...);
    public static *** d(...);
}
```

## **6. Patrón AppError: Mensajes Duales**

### 6.1. El problema de los mensajes de error

En una aplicación profesional, los errores tienen **dos audiencias diferentes**:

| Audiencia | Necesidad | Ejemplo |
|-----------|-----------|---------|
| **Usuario final** | Mensaje claro y accionable | "No se pudo conectar. Revisa tu internet." |
| **Developer** | Información técnica detallada | "IOException: timeout after 30s on api.example.com" |

**❌ Antipatrón común:**

```kotlin
// Muestra error técnico al usuario
catch (e: Exception) {
    SnackBar("SQLiteException: UNIQUE constraint failed: games.id")
    //       ↑ Usuario no entiende esto
}
```


### 6.2. Solución: AppError con mensajes duales

`AppError` es una **sealed class** que encapsula errores con dos propiedades:

```kotlin
sealed class AppError {
    abstract val userMessage: String       // Para UI (SnackBar)
    abstract val technicalMessage: String  // Para logs (Timber)
}
```


**Tipos de error disponibles:**

```kotlin
data class NetworkError(
    override val userMessage: String = "Connection problem. Check your internet.",
    override val technicalMessage: String
) : AppError()

data class DatabaseError(
    override val userMessage: String = "Error saving data. Try again.",
    override val technicalMessage: String
) : AppError()

data class NotFound(
    override val userMessage: String = "Content not found.",
    override val technicalMessage: String = "Resource not found (404)"
) : AppError()

data class ValidationError(
    override val userMessage: String,
    override val technicalMessage: String = userMessage
) : AppError()
```


### 6.3. Flujo completo

```
1. Repository detecta error
   ↓ Crea AppError con technicalMessage
   ↓ Logguea technicalMessage con Timber
   ↓ Retorna Resource.Error(appError)

2. ViewModel recibe error
   ↓ Logguea technicalMessage (contexto)
   ↓ Actualiza UI state con userMessage

3. UI muestra SnackBar
   ↓ Muestra userMessage al usuario
```


### 6.4. Tipos de AppError

| Tipo | Cuándo usar | userMessage default |
|------|-------------|---------------------|
| **NetworkError** | API, timeout, sin conexión | "Connection problem. Check your internet." |
| **DatabaseError** | Room, SQLite | "Error saving data. Try again." |
| **NotFound** | Recurso no existe (404) | "Content not found." |
| **Unauthorized** | Sin permisos (401/403) | "Access denied. Please login again." |
| **ValidationError** | Campos inválidos | *(Específico)* |
| **AuthError** | Firebase, login | "Authentication failed." |
| **Unknown** | No categorizado | "Something went wrong. Please try again." |

---

## **7. Implementación en Repository**

### 7.1. Ejemplo: GamesRepository

**`MockGamesRepositoryImpl.kt`**

```kotlin
override suspend fun getGameById(id: Int): Resource<Game> {
    return try {
        Timber.d("🎮 Buscando juego con ID: $id")
        simulateNetworkDelay()
        
        val game = dataSource.games.find { it.id == id }
        
        if (game != null) {
            Timber.i("✅ Juego encontrado: ${game.title}")
            Resource.Success(game)
        } else {
            // Error con mensaje dual
            val error = AppError.NotFound(
                technicalMessage = "Game not found with ID: $id"
            )
            Timber.w("⚠️ ${error.technicalMessage}")
            Resource.Error(error)
        }
    } catch (e: Exception) {
        // Excepción con mensaje dual
        val error = AppError.Unknown(
            technicalMessage = "Exception: ${e.message}"
        )
        Timber.e(e, "💥 ${error.technicalMessage}")
        Resource.Error(error)
    }
}
```


### 7.2. Ejemplo: toggleFavorite con AppError

```kotlin
override suspend fun toggleFavorite(gameId: Int): Resource<Unit> {
    return try {
        val user = getCurrentUser()
        Timber.d("⭐ Toggle favorito - gameId: $gameId")
        
        db.libraryDao().toggleFavorite(gameId)
        
        Timber.i("✅ Favorito actualizado")
        Resource.Success(Unit)
    } catch (e: SQLException) {
        val error = AppError.DatabaseError(
            technicalMessage = "SQLiteException: ${e.message}"
        )
        Timber.e(e, "💥 ${error.technicalMessage}")
        Resource.Error(error)
    }
}
```


**LogCat mostrará:**
```
D/GamesRepository: 🎮 Buscando juego con ID: 999
W/GamesRepository: ⚠️ Game not found with ID: 999
```


---

## **8. Implementación en UseCase**

**`GameUseCases.kt`**

```kotlin
suspend fun toggleFavorite(gameId: Int): Resource<Unit> {
    Timber.d("🎮 Toggle favorito para gameId: $gameId")
    
    return repository.toggleFavorite(gameId).also { result ->
        when (result) {
            is Resource.Success -> 
                Timber.i("✅ Toggle favorito exitoso")
            is Resource.Error -> 
                Timber.e("❌ Error: ${result.error.technicalMessage}")
            else -> {}
        }
    }
}
```


---

## **9. Implementación en ViewModel**

**`DetailViewModel.kt`**

```kotlin
fun loadGame(id: Int) {
    viewModelScope.launch {
        Timber.d("🎮 Cargando detalle del juego ID: $id")
        
        when (val result = useCases.getGameById(id)) {
            is Resource.Success -> {
                Timber.i("✅ Juego cargado: ${result.data.title}")
                _uiState.update { it.copy(game = result.data) }
            }
            is Resource.Error -> {
                // Log técnico
                Timber.e("❌ Error: ${result.error.technicalMessage}")
                
                // UI mensaje amigable
                _uiState.update { 
                    it.copy(errorMessage = result.error.userMessage)
                }
            }
        }
    }
}

fun toggleFavorite() {
    viewModelScope.launch {
        Timber.d("⭐ Usuario toggle favorito")
        
        when (val result = useCases.toggleFavorite(gameId)) {
            is Resource.Success -> {
                Timber.i("✅ Favorito actualizado")
            }
            is Resource.Error -> {
                Timber.e("❌ ${result.error.technicalMessage}")
                _uiState.update { 
                    it.copy(errorMessage = result.error.userMessage)
                }
            }
            else -> {}
        }
    }
}
```


---

## **10. Implementación en UI**

**`DetailScreen.kt`**

```kotlin
@Composable
fun DetailScreen(
    gameId: Int,
    viewModel: DetailViewModel = koinViewModel { parametersOf(gameId) }
) {
    val uiState by viewModel.uiState.collectAsState()
    val snackbarHostState = remember { SnackbarHostState() }

    // Mostrar errores en SnackBar
    LaunchedEffect(uiState.errorMessage) {
        uiState.errorMessage?.let { userMessage ->
            snackbarHostState.showSnackbar(
                message = userMessage,  // Mensaje amigable
                duration = SnackbarDuration.Short
            )
            viewModel.clearError()
        }
    }

    Scaffold(
        snackbarHost = { SnackbarHost(snackbarHostState) }
    ) { /* ... */ }
}
```


---

## **11. Ejemplo completo: Flujo de error**

### Escenario: Usuario intenta ver un juego inexistente

**1. Repository:**
```
D/GamesRepository: 🎮 Buscando juego con ID: 999
W/GamesRepository: ⚠️ Game not found with ID: 999
```


**2. UseCase:**
```
D/GameUseCases: 🎮 Obteniendo juego con ID: 999
W/GameUseCases: ⚠️ Juego no encontrado (ID: 999): Resource not found (404)
```


**3. ViewModel:**
```
D/DetailViewModel: 🎮 Cargando detalle del juego ID: 999
E/DetailViewModel: ❌ Error: Game not found with ID: 999
```


**4. UI (SnackBar):**
```
"Content not found."
```


---

## **12. Casos de uso por tipo de error**

### 12.1. NetworkError

```kotlin
try {
    val response = api.getGames()
    Resource.Success(response)
} catch (e: IOException) {
    val error = AppError.NetworkError(
        technicalMessage = "IOException: ${e.message}"
    )
    Timber.e(e, "💥 ${error.technicalMessage}")
    Resource.Error(error)
}
```


**LogCat:** `💥 IOException: Unable to resolve host`  
**SnackBar:** `"Connection problem. Check your internet."`

### 12.2. DatabaseError

```kotlin
try {
    db.gameDao().insert(game)
    Resource.Success(Unit)
} catch (e: SQLiteException) {
    val error = AppError.DatabaseError(
        technicalMessage = "SQLiteException: ${e.message}"
    )
    Timber.e(e, "💥 ${error.technicalMessage}")
    Resource.Error(error)
}
```


**LogCat:** `💥 SQLiteException: UNIQUE constraint failed`  
**SnackBar:** `"Error saving data. Try again."`

### 12.3. ValidationError

```kotlin
if (username.isBlank()) {
    val error = AppError.ValidationError(
        userMessage = "Username cannot be empty"
    )
    Timber.w("⚠️ ${error.userMessage}")
    _uiState.update { it.copy(errorMessage = error.userMessage) }
    return
}
```


**LogCat:** `⚠️ Username cannot be empty`  
**SnackBar:** `"Username cannot be empty"`

---

## **13. Personalización de mensajes**

```kotlin
// ✅ Usar default
AppError.NetworkError(
    technicalMessage = "IOException: ${e.message}"
)

// ✅ Personalizar userMessage
AppError.DatabaseError(
    userMessage = "Could not save your favorite game.",
    technicalMessage = "SQLiteException: UNIQUE constraint"
)

// ✅ ValidationError siempre requiere userMessage
AppError.ValidationError(
    userMessage = "Username must be at least 3 characters"
)
```


---

## **14. Buenas prácticas**

### ✅ QUÉ HACER

```kotlin
// ✅ Ofuscar datos sensibles
Timber.d("🔐 Login usuario: ${username.take(3)}***")

// ✅ Logs informativos con contexto
Timber.i("✅ 150 juegos cargados desde API")

// ✅ Contexto completo en errores
Timber.e(exception, "💥 Error al guardar juego ID: $gameId")

// ✅ Usar emojis para escaneo visual
Timber.d("⏳ Cargando...")
Timber.i("✅ Éxito")
Timber.w("⚠️ Advertencia")
Timber.e("❌ Error")
```


### ❌ QUÉ NO HACER

```kotlin
// ❌ Nunca passwords
Timber.d("Password: $password")

// ❌ Nunca datos personales
Timber.d("Email: $email, DNI: $dni")

// ❌ Logs en bucles
list.forEach { Timber.d("Item: $it") }
```


---

## **Resumen**

✅ **AppError** separa mensajes (usuario vs developer)  
✅ **Repository** logguea errores técnicos  
✅ **ViewModel** mapea y logguea contexto  
✅ **UI** muestra mensajes amigables  
✅ **Timber** facilita logging sin TAGs  
✅ **Emojis** mejoran escaneo en LogCat  
✅ **Mensajes duales** mantienen UX limpia y debugging detallado  

