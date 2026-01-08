# Gradle, AGP y Estructura de Proyecto en Android

Como desarrollador Android moderno, entender **Gradle** y la estructura de tu proyecto es tan importante como saber Kotlin. No es solo "magia" que hace que el botón de Play funcione; es el motor que compila, prueba y empaqueta tu aplicación.

En este documento, desmitificaremos estos conceptos y veremos cómo se organiza un proyecto profesional hoy en día.

## 1. ¿Qué son Gradle y AGP?

A menudo se confunden, pero son cosas distintas que trabajan juntas.

### Gradle: El Motor de Construcción

**Gradle** es un sistema de automatización de compilación de código abierto. Es agnóstico a la plataforma; se usa para Java, C++, Python, etc. Piensa en Gradle como un **gestor de tareas muy potente**. Sabe cómo:
- Descargar librerías de internet.
- Compilar código fuente.
- Ejecutar pruebas.
- Empaquetar resultados.

### AGP (Android Gradle Plugin): El Copiloto

Gradle por sí solo no sabe qué es un APK o un AndroidManifest. Aquí entra el **Android Gradle Plugin (AGP)**.
AGP es un plugin que se "enchufa" a Gradle y le enseña cómo construir aplicaciones Android específicamente. Le dice a Gradle: "Hey, estos archivos `.xml` son recursos y deben procesarse así", o "Este código Kotlin debe convertirse en bytecode Dalvik".

!!! note "Gradle Wrapper"
    En tu proyecto verás archivos `gradlew` (Linux/Mac) y `gradlew.bat` (Windows). Este es el **Wrapper**.
    **Siempre** usa el wrapper (`./gradlew build`) en lugar de instalar Gradle globalmente. Esto garantiza que todo el equipo (y el servidor de integración continua) use **exactamente la misma versión** de Gradle definida en `gradle/wrapper/gradle-wrapper.properties`.

---

## 2. Estructura de un Proyecto Moderno

Cuando abres Android Studio, por defecto ves la vista **"Android"**, que colapsa y organiza los archivos lógicamente. Sin embargo, para entender la estructura real, a veces es útil cambiar a la vista **"Project"**.

Un proyecto moderno tiene (principalmente) dos niveles de configuración: **Proyecto (Root)** y **Módulo (Module)**.

### Archivos de Configuración Clave

| Archivo | Nivel | Propósito |
| :--- | :--- | :--- |
| **`settings.gradle.kts`** | Root | Define **qué módulos** componen la app y de **dónde sacar los plugins**. Se ejecuta primero. |
| **`build.gradle.kts`** | Root | Configuración global para *todos* los módulos (limpieza, plugins comunes). Suele estar casi vacío ahora. |
| **`build.gradle.kts`** | Módulo (:app) | La configuración específica de tu app: versiones de SDK, dependencias, firma, etc. |
| **`libs.versions.toml`** | Gradle | **Version Catalog**. El lugar centralizado para definir versiones y librerías. |
| **`local.properties`** | Root | Configuración local de TU máquina (ruta del SDK, claves secretas). **Nunca se sube a Git**. |

---

## 3. Gestión de Dependencias: Version Catalogs

Desde hace poco, el estándar recomendado es usar **Version Catalogs**. En lugar de escribir versiones "a fuego" en cada `build.gradle.kts`, las definimos en un archivo TOML: `gradle/libs.versions.toml`.

Este archivo actúa como un inventario centralizado de todos los "ingredientes" (librerías y plugins) que usa tu app.

Estructura de `libs.versions.toml`:
```toml
[versions]
# 1. Aquí definimos los NÚMEROS de las versiones
kotlin = "1.9.0"
coreKtx = "1.10.1"
retrofit = "2.9.0"

[libraries]
# 2. Aquí definimos las LIBRERÍAS (Ingredientes básicos)
# Formato: grupo:nombre:versión
# 'version.ref' apunta a una variable definida arriba en [versions]
androidx-core-ktx = { group = "androidx.core", name = "core-ktx", version.ref = "coreKtx" }
retrofit = { group = "com.squareup.retrofit2", name = "retrofit", version.ref = "retrofit" }
retrofit-gson = { group = "com.squareup.retrofit2", name = "converter-gson", version.ref = "retrofit" }

[bundles]
# 3. (Opcional) Aquí agrupamos librerías que siempre van juntas
# Por ejemplo, para usar Retrofit siempre necesitas el core y un convertidor
networking = ["retrofit", "retrofit-gson"]

[plugins]
# 4. Aquí definimos los PLUGINS (Herramientas de construcción)
androidApplication = { id = "com.android.application", version.ref = "agp" }
```

### Ejemplo Práctico: ¿Cómo añado una librería nueva?

Imagina que quieres añadir **Coil** o **Glide** para cargar imágenes desde internet.

**Paso 1: Buscar la coordenada Maven**
Buscas en Google "Coil android library" y encuentras que la última versión es `2.4.0` y la coordenada es `io.coil-kt:coil:2.4.0`.

**Paso 2: Añadir al catálogo (`libs.versions.toml`)**
Editas el archivo `gradle/libs.versions.toml`:

```toml
[versions]
...
coil = "2.4.0" # <--- Añades la versión

[libraries]
...
coil = { group = "io.coil-kt", name = "coil", version.ref = "coil" } # <--- Defines la librería
```

**Paso 3: Sincronizar (Sync Gradle)**

Aparecerá una barrita amarilla en Android Studio: "Sync Now". Púlsala. Esto hace que Gradle lea el nuevo catálogo y genere las referencias.

**Paso 4: Usar en tu módulo (`app/build.gradle.kts`)**

Ahora vas a tu archivo de construcción y lo añades. Gracias al catálogo, ¡tendrás autocompletado!

```kotlin
dependencies {
    // ... otras dependencias
    implementation(libs.coil)
}
```

¡Y listo! Si mañana sale la versión `2.5.0` de Coil, solo tienes que cambiar el número en `[versions]` del archivo TOML, y se actualizará en todos los módulos que lo usen.

### El concepto de BOM (Bill of Materials)

En ecosistemas complejos como **Jetpack Compose** o **Firebase**, hay muchas librerías pequeñas (ui, material3, tooling, foundation...) que se actualizan independientemente pero que deben usarse en versiones **compatibles entre sí**. Gestionar esto a mano es una pesadilla.

Para solucionar esto existe el **BOM (Bill of Materials)**.

El BOM es una "super-librería" vacía que solo contiene una lista de versiones recomendadas que funcionan bien juntas. Cuando usas un BOM, **no indicas la versión de las librerías individuales**; Gradle mira el BOM para saber qué versión usar.

**Ejemplo con Compose:**

1.  En `libs.versions.toml` defines el BOM con versión, y las librerías **sin versión**.

```toml
[versions]
composeBom = "2023.08.00"

[libraries]
androidx-compose-bom = { group = "androidx.compose", name = "compose-bom", version.ref = "composeBom" }

# Fíjate que aquí NO hay 'version.ref'
androidx-ui = { group = "androidx.compose.ui", name = "ui" }
androidx-material3 = { group = "androidx.compose.material3", name = "material3" }
```

2.  En `build.gradle.kts` importas el BOM como `platform` y luego las librerías.

```kotlin
dependencies {
    // Importamos la PLATAFORMA (el BOM)
    implementation(platform(libs.androidx.compose.bom))

    // Importamos las librerías sin preocuparnos de la versión
    implementation(libs.androidx.ui)
    implementation(libs.androidx.material3)
}
```

Esto garantiza que si actualizas el BOM a `2023.10.00`, *todas* las librerías de Compose se actualizarán automáticamente a las versiones probadas y compatibles de esa release.

### ¿Qué librerías funcionan con BOM?

**No todas las librerías tienen BOM.**

El BOM se usa principalmente en ecosistemas grandes donde hay múltiples módulos que deben coordinarse.

*   **SÍ suelen tener BOM:**
    *   **Jetpack Compose**: (`androidx.compose:compose-bom`) Porque tiene ui, foundation, material, animation, etc.
    *   **Firebase**: (`com.google.firebase:firebase-bom`) Porque tiene auth, firestore, analytics, crashlytics, etc.
    *   **OkHttp / Retrofit**: A veces ofrecen BOM para gestionar sus familias de dependencias.
    *   **AWS Amplify**: Otro ecosistema gigante.

*   **NO suelen tener BOM:**
    *   Librerías individuales como **Glide**, **Coil**, **Lottie**, **Room** (aunque es parte de Jetpack, suele versionarse sola o seguir el ciclo de core).

### ¿Cómo sé si una librería tiene BOM?

No hay una "regla mágica", pero aquí tienes 3 formas de saberlo:

1.  **Documentación Oficial (La mejor)**: Cuando vas a la página de "Setup" o "Install" de Compose o Firebase, lo **primero** que te recomiendan es usar el BOM. Si la documentación te da directamente la línea `implementation("com.example:lib:1.0.0")`, es que probablemente no tenga BOM.
2.  **Maven Repository**: Si buscas la librería en Maven Central, a veces verás un artefacto que termina en `-bom` (ej. `compose-bom`).
3.  **El sentido común**: Si la librería es "una sola cosa" (ej. un calendario), no necesita BOM. Si es una "plataforma" (ej. Firebase), seguramente lo tenga.

### Resumen: Mezclando todo en un mismo proyecto

En un proyecto real, **siempre** tendrás una mezcla de ambas cosas. No hay conflicto, simplemente se declaran diferente en el catálogo y se importan diferente en el `build.gradle.kts`.

**Escenario Real**: Una app que usa **Jetpack Compose** (BOM), **Firebase** (BOM) y **Retrofit** (Normal/Sin BOM).

#### 1. `libs.versions.toml`
```toml
[versions]
# --- Versiones para BOMs ---
composeBom = "2023.08.00"
firebaseBom = "32.2.0"

# --- Versiones para Librerías Normales ---
retrofit = "2.9.0"
coil = "2.4.0"

[libraries]
# --- BOMs ---
androidx-compose-bom = { group = "androidx.compose", name = "compose-bom", version.ref = "composeBom" }
firebase-bom = { group = "com.google.firebase", name = "firebase-bom", version.ref = "firebaseBom" }

# --- Librerías DENTRO del BOM (SIN versión) ---
androidx-ui = { group = "androidx.compose.ui", name = "ui" }
androidx-material3 = { group = "androidx.compose.material3", name = "material3" }
firebase-analytics = { group = "com.google.firebase", name = "firebase-analytics" }

# --- Librerías NORMALES (CON versión) ---
retrofit = { group = "com.squareup.retrofit2", name = "retrofit", version.ref = "retrofit" }
coil = { group = "io.coil-kt", name = "coil", version.ref = "coil" }
```

#### 2. `app/build.gradle.kts`
```kotlin
dependencies {
    // 1. Importamos las Plataformas (BOMs)
    implementation(platform(libs.androidx.compose.bom))
    implementation(platform(libs.firebase.bom))

    // 2. Importamos las librerías del BOM (Sin versión)
    implementation(libs.androidx.ui)
    implementation(libs.androidx.material3)
    implementation(libs.firebase.analytics)

    // 3. Importamos las librerías Normales (Con versión en el catálogo)
    implementation(libs.retrofit)
    implementation(libs.coil)
}
```

---

## 4. Proyecto de Ejemplo: "MySimpleApp"

Vamos a diseccionar un proyecto real pero sencillo para ver cómo encajan todas las piezas. Imagina una app simple con una sola pantalla.

### 4.1. `settings.gradle.kts` (La entrada)

Este archivo le dice a Gradle cómo arrancar.

```kotlin
pluginManagement {
    repositories {
        google()
        mavenCentral()
        gradlePluginPortal()
    }
}
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
    }
}

rootProject.name = "MySimpleApp"

include(":app") // <--- Aquí decimos: "Este proyecto tiene un módulo llamado 'app'"
```

### 4.2. `gradle/libs.versions.toml` (El catálogo)
Aquí definimos nuestros ingredientes.

```toml
[versions]
agp = "8.1.1"
kotlin = "1.9.0"
coreKtx = "1.10.1"
appcompat = "1.6.1"
material = "1.9.0"
constraintlayout = "2.1.4"
junit = "4.13.2"

[libraries]
androidx-core-ktx = { group = "androidx.core", name = "core-ktx", version.ref = "coreKtx" }
androidx-appcompat = { group = "androidx.appcompat", name = "appcompat", version.ref = "appcompat" }
material = { group = "com.google.android.material", name = "material", version.ref = "material" }
androidx-constraintlayout = { group = "androidx.constraintlayout", name = "constraintlayout", version.ref = "constraintlayout" }
junit = { group = "junit", name = "junit", version.ref = "junit" }

[plugins]
androidApplication = { id = "com.android.application", version.ref = "agp" }
kotlinAndroid = { id = "org.jetbrains.kotlin.android", version.ref = "kotlin" }
```

### 4.3. `build.gradle.kts` (Nivel Proyecto)
Hoy en día es muy simple, solo registra los plugins para usarlos en los hijos.

```kotlin
// Build script de nivel superior (Top-level)
plugins {
    // Usamos 'apply false' porque solo queremos cargar las clases del plugin,
    // pero no aplicarlas a este proyecto raíz (se aplicarán en 'app').
    alias(libs.plugins.androidApplication) apply false
    alias(libs.plugins.kotlinAndroid) apply false
}
```

### 4.4. `app/build.gradle.kts` (Nivel Módulo)
Aquí es donde ocurre la magia de la configuración de la app.

```kotlin
plugins {
    alias(libs.plugins.androidApplication)
    alias(libs.plugins.kotlinAndroid)
}

android {
    namespace = "com.example.mysimpleapp"
    compileSdk = 33 // Versión del SDK usada para compilar (headers, nuevas APIs)

    defaultConfig {
        applicationId = "com.example.mysimpleapp"
        minSdk = 24 // Versión mínima de Android donde instalaremos la app (Android 7.0)
        targetSdk = 33 // Versión para la que hemos probado y diseñado la app
        versionCode = 1
        versionName = "1.0"

        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            isMinifyEnabled = false
            proguardFiles(getDefaultProguardFile("proguard-android-optimize.txt"), "proguard-rules.pro")
        }
    }
    
    // Necesario para usar características nuevas de Java/Kotlin
    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_1_8
        targetCompatibility = JavaVersion.VERSION_1_8
    }
    kotlinOptions {
        jvmTarget = "1.8"
    }
}

dependencies {
    // Referenciamos las librerías usando el catálogo (libs)
    implementation(libs.androidx.core.ktx)
    implementation(libs.androidx.appcompat)
    implementation(libs.material)
    implementation(libs.androidx.constraintlayout)

    // Tests unitarios
    testImplementation(libs.junit)
}
```

### Resumen
Esta estructura modular y centralizada parece verbosa al principio para un proyecto pequeño, pero es **vital** cuando el proyecto crece. Mantener las versiones en un solo lugar (`libs.versions.toml`) y separar la configuración del proyecto de la del módulo es la base del desarrollo profesional en Android.

---

## 5. Guía de Supervivencia: Conceptos del Día a Día

Para terminar, aquí tienes tres conceptos que usarás a diario y que suelen confundir al principio.

### 5.1. "Sync" vs "Build" (Sincronizar vs Construir)
*   **Sync (Sincronizar)**: Es rápido. Ocurre cuando cambias un archivo `.gradle.kts` o `.toml`. Gradle lee la configuración, descarga librerías nuevas y actualiza los índices de Android Studio. **No compila tu código Kotlin.**
    *   *Cuándo hacerlo*: Siempre que toques algo de la configuración de Gradle. Verás una barrita amarilla o un elefante con flechas azules.
*   **Build (Construir)**: Es lento. Compila todo tu código Kotlin, procesa los recursos y genera el APK.
    *   *Cuándo hacerlo*: Cuando le das al botón de "Play" (Run).

### 5.2. Clean & Rebuild (El "apagar y encender")
A veces, Android Studio "se lía". Te marca errores en rojo en código que sabes que está bien, o la app falla de forma rara.
*   **Build > Clean Project**: Borra la carpeta `build/` (los archivos temporales de compilación).
*   **Build > Rebuild Project**: Hace un Clean y luego un Build completo desde cero.
    *   *Truco del experto*: Si algo no tiene sentido, haz un `Clean Project`. Soluciona el 90% de los problemas "fantasmas".

### 5.3. Build Variants: Debug vs Release
Por defecto, cuando le das al Play, estás instalando la variante **Debug**.
*   **Debug**:
    *   SE PUEDE depurar (breakpoints).
    *   No está optimizada (es más lenta).
    *   Se firma con una clave de prueba insegura.
*   **Release**:
    *   NO se puede depurar (normalmente).
    *   Está optimizada y ofuscada (R8/Proguard).
    *   Se firma con tu clave real para subirla a Google Play.

Puedes cambiar de variante en la pestaña **Build Variants** (normalmente a la izquierda-abajo en Android Studio). ¡No intentes subir una build Debug a la Play Store!

---

## 6. Compatibilidad y Versiones de Java

Un dolor de cabeza común es cuando las versiones no coinciden. Gradle, el Plugin de Android (AGP) y Java tienen una relación estricta.

### 6.1. La Matriz de Compatibilidad
No puedes usar cualquier versión de Gradle con cualquier versión de AGP.
*   **AGP 8.0+** requiere **Gradle 8.0+** y **Java 17**.
*   **AGP 7.4** requiere **Gradle 7.5+** y **Java 11**.

> **Consejo**: Si actualizas Android Studio, a menudo te propondrá actualizar el AGP y Gradle automáticamente. Acepta si tu equipo está de acuerdo, pero ten cuidado si saltas muchas versiones de golpe.

Puedes consultar la tabla oficial siempre actualizada aquí: [Android Gradle Plugin version requirements](https://developer.android.com/build/releases/gradle-plugin?hl=es-419#compatibility).

### 6.2. ¿Qué Java usa mi proyecto?
Hay **dos** configuraciones de Java que debes distinguir:

1.  **Java para compilar tu código (Target JVM)**:
    Define qué características de Java puede usar tu código Kotlin/Java y en qué dispositivos funcionará.
    Se configura en `app/build.gradle.kts`:
    ```kotlin
    android {
        compileOptions {
            sourceCompatibility = JavaVersion.VERSION_1_8
            targetCompatibility = JavaVersion.VERSION_1_8
        }
        kotlinOptions {
            jvmTarget = "1.8"
        }
    }
    ```
    *Nota: A partir de AGP 8, por defecto suele ser Java 17, aunque muchos proyectos siguen en 1.8 (Java 8) para máxima compatibilidad con móviles antiguos.*

2.  **Java para ejecutar Gradle (Gradle Daemon)**:
    Es la versión de Java que usa tu ordenador para *correr* el proceso de construcción.
    *   **Cómo cambiarlo**:
        1.  Ve a `File > Settings` (o `Android Studio > Settings` en Mac).
        2.  Busca `Build, Execution, Deployment > Build Tools > Gradle`.
        3.  En **Gradle JDK**, selecciona la versión. Normalmente deberías usar la versión "Embedded" que trae Android Studio, o asegurarte de que sea al menos Java 17 para proyectos modernos.

!!! warning "El aviso de diferentes JDKs"
    
    A veces Android Studio te mostrará un aviso diciendo que **"Gradle JDK"** y el **"Project JDK"** son diferentes.
    
    Esto ocurre porque Android Studio intenta usar el mismo Java para indexar tu código que Gradle usa para compilarlo. Si son diferentes, se desperdicia memoria (se arrancan dos máquinas virtuales Java) y pueden surgir errores extraños de resolución de tipos.
    
    **Solución**: Intenta que ambos apunten a la misma instalación.
    
    1.  **Gradle JDK**: Ve a `Settings > Build... > Build Tools > Gradle`.
    2.  **Project SDK**: Ve a `File > Project Structure... > Project Settings > Project`.
    3.  Asegúrate de que el **SDK** seleccionado en el paso 2 coincide con la versión elegida en el paso 1 (por ejemplo, seleccionando `jbr-17` o `Embedded JDK` en ambos sitios).


### 6.3. ¿Dónde miro qué versiones tengo instaladas?

A veces te preguntarán "¿Qué versión de AGP usas?" y no sabrás dónde mirar. Aquí tienes la "chuleta":

*   **Gradle Version**:
    *   Mira en `gradle/wrapper/gradle-wrapper.properties`.
    *   Busca la línea `distributionUrl=.../gradle-8.0-bin.zip`. El número `8.0` es tu versión.
*   **AGP Version**:
    *   Mira en `libs.versions.toml` bajo `[versions]`, la variable `agp`.
    *   O en `build.gradle.kts` (Project level) en el bloque `plugins`.
*   **Kotlin Version**:
    *   Mira en `libs.versions.toml` bajo `[versions]`, la variable `kotlin`.
    *   O en `build.gradle.kts` (Project level) en el bloque `plugins`.

Alternativamente, en Android Studio, puedes ir a **File > Project Structure > Project**, y allí verás un resumen gráfico con todas estas versiones.



