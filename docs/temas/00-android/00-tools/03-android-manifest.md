# El Manifiesto de la App: `AndroidManifest.xml`

Si `build.gradle` es el jefe de obra que construye el edificio, el `AndroidManifest.xml` es el **DNI o Pasaporte** de tu aplicación. 

Es la primera fuente de verdad que el sistema operativo Android consulta para saber qué hay dentro de tu APK/Bundle y qué permisos pretendes usar.

---

## 1. Permisos y Hardware: Declarando intenciones

Antes de que el sistema te deje entrar en escena, debes declarar qué recursos del teléfono vas a utilizar.

*   **`<uses-permission>`**: Solicitas permiso para acceder a datos o funciones restringidas.
    *   `INTERNET`: Para conectarte a servicios externos.
    *   `ACCESS_FINE_LOCATION`: Para saber la posición GPS exacta del usuario.
    *   `CAMERA`: Para tomar fotos o vídeos.
*   **`<uses-feature>`**: Informas sobre el hardware que tu app **necesita** para funcionar.
    *   Ejemplo: `<uses-feature android:name="android.hardware.camera" android:required="true" />`. 
    *   **Importante**: Si marcas `required="true"`, la Google Play Store **ocultará** tu app a los usuarios cuyo móvil no tenga ese hardware.

---

## 2. El Bloque `<application>`: El contenedor global

Este bloque define las propiedades de alto nivel de toda tu aplicación. Es como la configuración general del sistema.

```xml
<application
    android:name=".MyApplication"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:theme="@style/Theme.MiApp"
    android:allowBackup="true"
    android:supportsRtl="true"
    android:usesCleartextTraffic="false">
    
    <!-- Componentes aquí inside... -->
</application>
```

### Elementos clave explicados:
*   **`android:name`**: (Opcional) Referencia a una clase Java/Kotlin que hereda de `Application`. Es el primer código que se ejecuta al abrir la app, ideal para inicializar librerías globales (como bases de datos o inyección de dependencias).
*   **`android:theme`**: Define la "estética" por defecto. Referencia a un fichero de estilos (normalmente en `res/values/themes.xml`). Aquí se decide si usas **Material Design 3**, los colores principales de tu marca, o si tu app soporta modo oscuro de forma nativa.
*   **`android:usesCleartextTraffic`**: Si se pone en `false` (recomendado), Android bloqueará todas las peticiones HTTP no seguras, obligándote a usar **HTTPS**. Es vital para la seguridad actual.

---

## 3. Los "4 Fantásticos": Los Componentes de Android

Android no es un programa monolítico; se compone de piezas intercambiables y declarables. Hay 4 tipos principales de componentes:

1.  **`<activity>` (Actividades)**: Son las **pantallas**. Cada pantalla que el usuario ve debe estar declarada aquí. Es el componente con el que el usuario interactúa directamente.
2.  **`<service>` (Servicios)**: Componentes que corren en **segundo plano** sin interfaz de usuario. Por ejemplo, una app de música que sigue sonando cuando apagas la pantalla o una descarga pesada.
3.  **`<receiver>` (Broadcast Receivers)**: Son "orejas" que escuchan eventos del sistema. Por ejemplo, una app que se activa cuando el móvil se conecta a un cargador o cuando termina de arrancar el sistema (`BOOT_COMPLETED`).
4.  **`<provider>` (Content Providers)**: Permiten **compartir datos** entre aplicaciones de forma segura. Por ejemplo, la lista de contactos del teléfono o la galería de fotos se exponen mediante Content Providers.

---

## 4. Intent Filters: La "Publicidad" de tus capacidades

Los `intent-filter` son etiquetas que pones a tus componentes para decirle al sistema: *"Yo sé hacer esto"*.

No solo sirven para marcar la pantalla de inicio. Un `intent-filter` descompone en tres partes:

1.  **`<action>`**: El **qué**. ¿Quieres ver un dato (`VIEW`)?, ¿enviar algo (`SEND`)?, ¿editar algo (`EDIT`)?
2.  **`<category>`**: El **contexto**. ¿Es la puerta principal (`LAUNCHER`)?, ¿es algo que se abre desde el navegador (`BROWSABLE`)?
3.  **`<data>`**: El **formato**. ¿Manejas archivos PDF?, ¿URLs que empiecen por `https://miweb.com`?, ¿imágenes PNG?

### Ejemplo: Abriendo nuestra app desde un enlace web (Deep Linking)
```xml
<activity android:name=".ProductDetailActivity" android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <!-- Aceptamos enlaces que sean https://www.tienda.com/productos/... -->
        <data android:scheme="https" android:host="www.tienda.com" android:pathPrefix="/productos" />
    </intent-filter>
</activity>
```

!!! tip "Diferencia Vital"
    Si declaras una Activity pero no le pones ningún `intent-filter`, la Activity sigue existiendo, pero solo podrá ser abierta **desde dentro de tu propia app**. Sin filtros, el "mundo exterior" no sabe cómo llegar a ella.

---

## 5. Referencias de documentación oficial

Para profundizar en cada etiqueta y atributo, puedes consultar la documentación oficial de Android:

*   [Descripción general del manifiesto de la aplicación](https://developer.android.com/guide/topics/manifest/manifest-intro?hl=es-419): Conceptos básicos y estructura.
*   [Referencia del elemento `<application>`](https://developer.android.com/guide/topics/manifest/application-element?hl=es-419): Lista exhaustiva de todos los atributos configurables.
*   [Guía de Intenciones y Filtros de Intenciones](https://developer.android.com/guide/components/intents-filters?hl=es-419): Cómo funciona la comunicación entre componentes.
*   [Referencia de permisos de Android](https://developer.android.com/reference/android/Manifest.permission): Diccionario de todos los permisos disponibles en el sistema.
