# **20. Logging Avanzado: Crashlytics, ProGuard y Analytics**

En esta guía configuraremos herramientas avanzadas de logging para producción: Firebase Crashlytics para monitoreo de errores, ProGuard para optimización, y Firebase Analytics para métricas de uso.

!!! tip "Repositorio de la Aplicación"
    El código fuente de la aplicación se encuentra en el repositorio de GitHub: [MyGameStore](https://github.com/jssdocente/MyGameStore)

#### Resumen

1. Firebase Crashlytics: Monitoreo de errores en producción
2. Configuración de ProGuard/R8 para release
3. Firebase Analytics: Métricas de uso
4. Logging remoto: Envío de logs a servicios cloud
5. Buenas prácticas de monitoreo

---

## **1. Firebase Crashlytics**

### 1.1. ¿Qué es Crashlytics?

**Crashlytics** es un servicio de Firebase que captura y reporta crashes y errores en producción en tiempo real.

**Características:**
- 📊 Dashboard con estadísticas de crashes
- 🔍 Stack traces detallados
- 👥 Usuarios afectados
- 📱 Información del dispositivo
- ⚡ Alertas en tiempo real
- 🆓 Gratis (incluido en Firebase)

### 1.2. Agregar dependencia

**`app/build.gradle.kts`**

```kotlin
plugins {
    // ... plugins existentes
    alias(libs.plugins.google.services)
    id("com.google.firebase.crashlytics") // Nuevo
}

dependencies {
    // ... dependencias existentes
    
    // Firebase Crashlytics
    implementation(platform(libs.firebase.bom))
    implementation("com.google.firebase:firebase-crashlytics")
}
```


**`gradle/libs.versions.toml`**

```toml
[versions]
# ... versiones existentes
firebaseCrashlytics = "3.0.1"

[plugins]
# ... plugins existentes
firebase-crashlytics = { id = "com.google.firebase.crashlytics", version.ref = "firebaseCrashlytics" }
```


### 1.3. Configurar CrashReportingTree

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

/**
 * Árbol personalizado que envía logs a Crashlytics
 */
class CrashReportingTree : Timber.Tree() {
    private val crashlytics = FirebaseCrashlytics.getInstance()
    
    override fun log(priority: Int, tag: String?, message: String, t: Throwable?) {
        if (priority == Log.VERBOSE || priority == Log.DEBUG) {
            return // Ignorar logs de desarrollo
        }
        
        // Registrar mensaje en Crashlytics
        crashlytics.log("$tag: $message")
        
        // Si hay excepción, registrarla
        t?.let {
            crashlytics.recordException(it)
        }
    }
}
```


### 1.4. Uso en Repository

```kotlin
override suspend fun getGameById(id: Int): Resource<Game> {
    return try {
        Timber.d("🎮 Buscando juego ID: $id")
        val game = dataSource.games.find { it.id == id }
        
        if (game != null) {
            Resource.Success(game)
        } else {
            val error = AppError.NotFound(
                technicalMessage = "Game not found with ID: $id"
            )
            Timber.w("⚠️ ${error.technicalMessage}")
            Resource.Error(error)
        }
    } catch (e: Exception) {
        val error = AppError.Unknown(
            technicalMessage = "Exception: ${e.message}"
        )
        
        // 🔥 Se enviará a Crashlytics automáticamente
        Timber.e(e, "💥 ${error.technicalMessage}")
        
        Resource.Error(error)
    }
}
```


### 1.5. Logs personalizados en Crashlytics

```kotlin
// Añadir contexto del usuario
FirebaseCrashlytics.getInstance().apply {
    setUserId(username)
    setCustomKey("screen", "DetailScreen")
    setCustomKey("game_id", gameId)
}

// Registrar evento importante
FirebaseCrashlytics.getInstance().log("Usuario marcó favorito: gameId=$gameId")
```


### 1.6. Forzar crash de prueba

```kotlin
// Solo para testing (NUNCA en producción real)
if (BuildConfig.DEBUG) {
    Button(onClick = { 
        throw RuntimeException("Test Crash") 
    }) {
        Text("Test Crashlytics")
    }
}
```


---

## **2. ProGuard/R8: Optimización para Release**

### 2.1. ¿Qué es R8?

**R8** es el optimizador de código de Android que:
- 🗜️ **Minimiza** el código (reduce tamaño)
- 🔐 **Ofusca** nombres de clases y métodos
- ⚡ **Optimiza** el rendimiento
- 🗑️ **Elimina** código no usado (incluidos logs)

### 2.2. Habilitar R8 en release

**`app/build.gradle.kts`**

```kotlin
android {
    buildTypes {
        release {
            isMinifyEnabled = true
            isShrinkResources = true
            proguardFiles(
                getDefaultProguardFile("proguard-android-optimize.txt"),
                "proguard-rules.pro"
            )
        }
    }
}
```


### 2.3. Configurar ProGuard rules

**`app/proguard-rules.pro`**

```
# Eliminar logs de Timber en release
-assumenosideeffects class timber.log.Timber {
    public static *** v(...);
    public static *** d(...);
}

# Mantener información de líneas para Crashlytics
-keepattributes SourceFile,LineNumberTable

# Renombrar archivos fuente (ofuscación adicional)
-renamesourcefileattribute SourceFile

# Mantener clases de modelos (para Gson/Serialization)
-keep class com.pmdm.mygamestore.domain.model.** { *; }

# Mantener clases de Room
-keep class * extends androidx.room.RoomDatabase
-keep @androidx.room.Entity class *
-dontwarn androidx.room.paging.**

# Mantener Firebase
-keep class com.google.firebase.** { *; }
-keep class com.google.android.gms.** { *; }

# Mantener Koin
-keep class org.koin.** { *; }
-keepclassmembers class ** {
    @org.koin.core.annotation.* <methods>;
}

# Mantener Retrofit (si se usa)
-keepattributes Signature
-keepattributes *Annotation*
-keep class retrofit2.** { *; }
```


### 2.4. Verificar eliminación de logs

**Después de compilar release:**

1. Genera APK: `./gradlew assembleRelease`
2. Descompila con jadx-gui
3. Verifica que los logs `Timber.d()` y `Timber.v()` no aparecen
4. Los `Timber.e()` y `Timber.w()` deben permanecer

---

## **3. Firebase Analytics**

### 3.1. Configuración

**`app/build.gradle.kts`**

```kotlin
dependencies {
    // Firebase Analytics
    implementation(platform(libs.firebase.bom))
    implementation("com.google.firebase:firebase-analytics")
}
```


### 3.2. Registrar eventos personalizados

**`GameUseCases.kt`**

```kotlin
class GameUseCases(
    private val repository: GamesRepository,
    private val analytics: FirebaseAnalytics
) {
    suspend fun toggleFavorite(gameId: Int): Resource<Unit> {
        Timber.d("⭐ Toggle favorito: $gameId")
        
        return repository.toggleFavorite(gameId).also { result ->
            when (result) {
                is Resource.Success -> {
                    // Registrar evento en Analytics
                    analytics.logEvent("game_favorited") {
                        param("game_id", gameId.toLong())
                        param("action", "add")
                    }
                    Timber.i("✅ Favorito actualizado")
                }
                is Resource.Error -> {
                    Timber.e("❌ Error: ${result.error.technicalMessage}")
                }
                else -> {}
            }
        }
    }
}
```


### 3.3. Inyectar Analytics con Koin

**`DomainModule.kt`**

```kotlin
val domainModule = module {
    // Firebase Analytics
    single { Firebase.analytics }
    
    // UseCases con Analytics
    single { GameUseCases(get(), get()) }
}
```


### 3.4. Eventos importantes a registrar

```kotlin
// Login exitoso
analytics.logEvent(FirebaseAnalytics.Event.LOGIN) {
    param(FirebaseAnalytics.Param.METHOD, "email")
}

// Búsqueda de juegos
analytics.logEvent(FirebaseAnalytics.Event.SEARCH) {
    param(FirebaseAnalytics.Param.SEARCH_TERM, query)
}

// Ver detalle de juego
analytics.logEvent(FirebaseAnalytics.Event.SELECT_ITEM) {
    param(FirebaseAnalytics.Param.ITEM_ID, gameId.toString())
    param(FirebaseAnalytics.Param.ITEM_NAME, game.title)
}

// Compartir juego
analytics.logEvent(FirebaseAnalytics.Event.SHARE) {
    param(FirebaseAnalytics.Param.CONTENT_TYPE, "game")
    param(FirebaseAnalytics.Param.ITEM_ID, gameId.toString())
}
```


---

## **4. Logging Remoto: Custom Backend**

### 4.1. Enviar logs a servidor propio

```kotlin
class RemoteLogTree(
    private val apiService: LoggingApiService
) : Timber.Tree() {
    
    private val logQueue = mutableListOf<LogEntry>()
    
    override fun log(priority: Int, tag: String?, message: String, t: Throwable?) {
        if (priority < Log.WARN) return
        
        // Encolar log
        logQueue.add(
            LogEntry(
                level = priority,
                tag = tag ?: "Unknown",
                message = message,
                exception = t?.stackTraceToString(),
                timestamp = System.currentTimeMillis()
            )
        )
        
        // Enviar batch cada 10 logs
        if (logQueue.size >= 10) {
            sendLogs()
        }
    }
    
    private fun sendLogs() {
        CoroutineScope(Dispatchers.IO).launch {
            try {
                apiService.sendLogs(logQueue.toList())
                logQueue.clear()
            } catch (e: Exception) {
                // No hacer nada si falla
            }
        }
    }
}

data class LogEntry(
    val level: Int,
    val tag: String,
    val message: String,
    val exception: String?,
    val timestamp: Long
)
```


### 4.2. API para logs

```kotlin
interface LoggingApiService {
    @POST("logs/batch")
    suspend fun sendLogs(@Body logs: List<LogEntry>): Response<Unit>
}
```


---

## **5. Monitoreo de rendimiento**

### 5.1. Timber con métricas

```kotlin
class PerformanceTree : Timber.Tree() {
    private val startTimes = mutableMapOf<String, Long>()
    
    override fun log(priority: Int, tag: String?, message: String, t: Throwable?) {
        // Detectar inicio de operación
        if (message.startsWith("⏳")) {
            startTimes[tag ?: ""] = System.currentTimeMillis()
        }
        
        // Detectar fin de operación
        if (message.startsWith("✅")) {
            val startTime = startTimes.remove(tag ?: "")
            if (startTime != null) {
                val duration = System.currentTimeMillis() - startTime
                Timber.i("⏱️ $tag: ${duration}ms")
                
                // Enviar a Firebase Performance
                val trace = Firebase.performance.newTrace(tag ?: "operation")
                trace.putMetric("duration_ms", duration)
                trace.stop()
            }
        }
    }
}
```


### 5.2. Uso en código

```kotlin
suspend fun loadGames() {
    Timber.d("⏳ Cargando juegos")
    
    val games = repository.getAllGames()
    
    Timber.i("✅ Juegos cargados")
    // Automáticamente se registrará el tiempo
}
```


---

## **6. Dashboard de monitoreo**

### 6.1. Firebase Console

Accede a: `console.firebase.google.com`

**Crashlytics:**
- Crashes por versión
- Usuarios afectados
- Stack traces
- Tendencias

**Analytics:**
- Usuarios activos
- Eventos personalizados
- Retención
- Flujos de usuario

**Performance:**
- Tiempos de respuesta
- Network requests
- Screen rendering

### 6.2. Configurar alertas

```kotlin
// En Firebase Console → Alerts
- Nuevo crash detectado
- Crash rate > 1%
- Usuarios afectados > 100
```


---

## **7. Testing de logs**

### 7.1. Unit test con Timber

```kotlin
class LoggingTest {
    
    @Before
    fun setup() {
        Timber.plant(object : Timber.DebugTree() {
            override fun log(priority: Int, tag: String?, message: String, t: Throwable?) {
                println("[$priority] $tag: $message")
            }
        })
    }
    
    @Test
    fun `verify error is logged`() {
        val repository = GamesRepositoryImpl()
        
        runBlocking {
            val result = repository.getGameById(999)
            
            assertTrue(result is Resource.Error)
            // Verificar que se logueó el error
        }
    }
}
```


---

## **8. Comparación de herramientas**

| Herramienta | Propósito | Costo | Cuándo usar |
|-------------|-----------|-------|-------------|
| **Timber** | Logging local | Gratis | Desarrollo |
| **Crashlytics** | Crashes remotos | Gratis | Producción (recomendado) |
| **Firebase Analytics** | Métricas de uso | Gratis | Todas las apps |
| **Sentry** | Errores + Performance | Pago | Apps enterprise |
| **Bugsnag** | Errores + Alertas | Pago | Apps críticas |
| **Custom backend** | Control total | Infraestructura | Casos específicos |

---

## **9. Checklist de producción**

Antes de publicar en Play Store:

- [ ] ✅ Timber configurado con CrashReportingTree
- [ ] ✅ ProGuard habilitado (`isMinifyEnabled = true`)
- [ ] ✅ Logs DEBUG/VERBOSE eliminados
- [ ] ✅ Crashlytics integrado
- [ ] ✅ Analytics configurado
- [ ] ✅ No hay datos sensibles en logs
- [ ] ✅ Alertas configuradas en Firebase
- [ ] ✅ Mapping file guardado para desofuscar crashes
- [ ] ✅ Testing de crash reports

---

## **Resumen**

✅ **Crashlytics** monitorea crashes en producción  
✅ **ProGuard/R8** elimina logs y optimiza APK  
✅ **Analytics** rastrea uso de la app  
✅ **Logging remoto** para control avanzado  
✅ **Performance monitoring** para métricas  
✅ **Dashboard** centralizado en Firebase Console  

**Stack completo de logging:**

```
Desarrollo → Timber.DebugTree() → LogCat
Producción → CrashReportingTree() → Crashlytics → Firebase Console
```


---

¿Quieres que continúe con algún tema adicional o pasamos a implementar esto en el proyecto?