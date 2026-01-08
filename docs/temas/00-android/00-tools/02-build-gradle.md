# El Fichero de Construcción: `build.gradle.kts`

En el corazón de la compilación de tu aplicación Android se encuentra el fichero `build.gradle.kts` (o `build.gradle` si usas Groovy, aunque el estándar moderno es **Kotlin DSL**).

Este fichero define **CÓMO** se debe construir tu aplicación: desde qué versión de Android soporta, hasta qué librerías externas utiliza.

!!! note "Project vs Module"
    Recuerda que existen dos niveles de `build.gradle`:
    
    1.  **Project-level (`build.gradle.kts` raíz):** Define configuración global para *todos* los módulos (normalmente solo plugins comunes).
    2.  **Module-level (`app/build.gradle.kts`):** Es donde pasarás el 99% del tiempo. Define la configuración específica de tu app (o módulo). **Nos centraremos en este.**

---

## Estructura Anatómica

Un fichero `build.gradle.kts` de módulo típico tiene esta estructura:

```kotlin
plugins {
    // 1. Plugins
}

android {
    // 2. Configuración Android
}

dependencies {
    // 3. Dependencias externas
}
```

Vamos a diseccionar cada parte.

### 1. Bloque `plugins`

Los plugins son extensiones que "enseñan" a Gradle cómo hacer cosas nuevas. Por defecto, Gradle no sabe qué es una "App Android". Necesitamos aplicarle el plugin de Android.

```kotlin
plugins {
    // Le dice a Gradle: "Este módulo es una Aplicación Android"
    alias(libs.plugins.android.application)
    
    // Le dice a Gradle: "Vamos a usar Kotlin en Android"
    alias(libs.plugins.kotlin.android)
}
```

!!! tip "Version Catalogs (`libs.*`)"
    Fíjate en el uso de `alias(libs...)`. Esto es **Version Catalogs**. En lugar de escribir el ID del plugin y la versión "a fuego" (hardcoded), referenciamos una definición centralizada en el fichero `libs.versions.toml`. ¡Es mucho más limpio y fácil de mantener!

### 2. Bloque `android`

Aquí configuramos todo lo relacionado con el SDK de Android.

#### Namespace y Compilación
```kotlin
android {
    // Identificador único de tu paquete (sustituye al antiguo 'package' en Manifest)
    namespace = "com.ejemplo.miapp"
    
    // La versión del SDK que usas para COMPILAR el código.
    // Te permite usar las APIs más nuevas de esa versión en tu código.
    compileSdk = 35 
    
    // ...
}
```

#### Default Config
La configuración base que se aplica a todas las versiones de tu app (debug, release, etc.).

```kotlin
    defaultConfig {
        // ID único de la aplicación en la Play Store.
        applicationId = "com.ejemplo.miapp"
        
        // Mínima versión de Android donde funciona tu app.
        // Si pones 24 (Android 7.0), nadie con Android 6.0 podrá instalarla.
        minSdk = 24
        
        // Versión objetivo. Le dice a Android: "He probado mi app hasta esta versión".
        // Permite que el sistema active optimizaciones o comportamientos nuevos.
        // Lo ideal es: targetSdk == compileSdk
        targetSdk = 35
        
        // Versionado para la tienda (interno y visible).
        versionCode = 1
        versionName = "1.0"
        
        // Runner para los tests instrumentados
        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
    }
```

#### Build Types (Tipos de Construcción)
Define perfiles de compilación. Por defecto siempre tienes `debug` y `release`.

```kotlin
    buildTypes {
        release {
            // ¿Activamos minificación (R8)? (Borrar código no usado, ofuscar...)
            isMinifyEnabled = false 
            
            // Reglas de ProGuard para la ofuscación
            proguardFiles(
                getDefaultProguardFile("proguard-android-optimize.txt"),
                "proguard-rules.pro"
            )
        }
        // El bloque 'debug' existe implícitamente aunque no lo escribas.
    }
```

#### Opciones de Compilación
Configuramos la versión de Java que usaremos. Hoy en día, Java 11 o 17 son el estándar para desarrollo Android moderno.

```kotlin
    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_11
        targetCompatibility = JavaVersion.VERSION_11
    }
    kotlinOptions {
        jvmTarget = "11"
    }
```

### 3. Bloque `dependencies`

Aquí listamos las librerías externas que nuestra app necesita para funcionar.

```kotlin
dependencies {
    // Dependencias 'core' (se compilan y empaquetan con la app)
    implementation(libs.androidx.core.ktx)
    implementation(libs.androidx.appcompat)
    implementation(libs.material)
    
    // Dependencias SOLO para Tests Unitarios (JUnit local)
    // No se empaquetan en la APK final.
    testImplementation(libs.junit)
    
    // Dependencias puramente de Tests Instrumentados (Android)
    androidTestImplementation(libs.androidx.junit)
    androidTestImplementation(libs.androidx.espresso.core)
    
    // Herramientas de debug (solo en builds debug)
    // debugImplementation(libs.leakcanary)
}
```

#### Tipos de dependencia comunes
*   **`implementation`**: La más común. La librería está disponible en compilación y ejecución.
*   **`testImplementation`**: Solo disponible dentro de `src/test` (tests unitarios locales).
*   **`androidTestImplementation`**: Solo disponible dentro de `src/androidTest` (tests en dispositivo).
*   **`ksp` (o `kapt`)**: Para procesadores de anotaciones (como Room o Dagger/Hilt).
*   **`debugImplementation`**: Solo se incluye en la variante `debug`.

!!! warning "Cuidado con las versiones"
    Evita mezclar versiones de librerías relacionadas (como las de `androidx.lifecycle` o `kotlinx.coroutines`). Usar el **Version Catalog** (`libs.versions.toml`) ayuda mucho a mantener la coherencia, ya que defines la versión en un solo lugar.
