# 📄 Proyecto "MyGameStore"

!!! tip "Repositorio de la Aplicación"
    El código fuente de la aplicación se encuentra en el repositorio de GitHub: [MyGameStore](https://github.com/jssdocente/MyGameStore)
    

## 1. Visión General

**MyGameStore** es una aplicación nativa de Android diseñada para amantes de los videojuegos. El objetivo principal es ofrecer un catálogo online actualizado (consultando la API de RAWG) y permitir a los usuarios gestionar su propia **biblioteca personal** de forma local.

El reto principal de este proyecto no es solo que la aplicación "funcione", sino que esté construida bajo los estándares de la industria actual: **Escalabilidad, Modularidad y Código Limpio**.

## 2. Funcionalidad de la Aplicación

La aplicación simulará un entorno multi-usuario en un mismo dispositivo, gestionando la persistencia de datos y sesiones.

### 🔐 Módulo de Autenticación (Local)
*   **Login:** El usuario debe poder iniciar sesión con sus credenciales.
*   **Registro:** Posibilidad de crear una cuenta nueva. Los datos se guardan en la base de datos interna del dispositivo.
*   **Gestión de Sesión:** La aplicación recordará al usuario activo. Si cierras la app y vuelves a entrar, no debes loguearte de nuevo (Auto-login).

### 🎮 Módulo de Exploración (Remoto)
*   **Catálogo:** Visualización de una lista de videojuegos populares obtenidos de internet.
*   **Detalle:** Al pulsar en un juego, se muestra información detallada: descripción, fecha de lanzamiento, imagen en alta calidad y valoración.

### 📚 Módulo de Biblioteca (Local & Privado)
*   **Favoritos:** El usuario puede marcar/desmarcar juegos como favoritos desde el detalle.
*   **Privacidad de Datos:**
    *   Si el *Usuario A* guarda "Zelda", y luego inicia sesión el *Usuario B*, el *Usuario B* **NO** debe ver "Zelda" en su lista.
    *   Cada biblioteca es exclusiva del usuario logueado.

---

## 3. Requisitos Técnicos (El "Tech Stack")

Para este proyecto utilizaremos las tecnologías más demandadas actualmente en el desarrollo Android moderno.

### 🎨 Interfaz de Usuario (UI)
*   **Jetpack Compose:** Todo el diseño se realizará de forma declarativa.
*   **Material Design 3:** Uso de temas (Claro/Oscuro), tipografías y componentes estándar.
*   **Navegación por Estado (Navigation 3):** No usaremos el `NavHost` clásico basado en XML o Strings. Implementaremos un sistema de navegación robusto basado en una pila de estado (`mutableStateListOf`) y objetos tipados (`AppRoutes`).

### 🏗️ Arquitectura
El proyecto debe seguir estrictamente **Clean Architecture** separando el código en tres capas:
1.  **Presentation (UI):** Composables y ViewModels.
2.  **Domain:** Casos de uso (Lógica de negocio pura) y Modelos.
3.  **Data:** Repositorios, Fuentes de datos (API y BBDD) y Mappers.

### 💉 Inyección de Dependencias
*   **Koin:** Se utilizará para gestionar la creación e inyección de componentes (Repositorios en ViewModels, Retrofit en Repositorios, etc.).

### 💾 Gestión de Datos
*   **Remoto (API):** **Retrofit 2** para consumir la API de [RAWG.io](https://rawg.io/apidocs).
*   **Local (BBDD):** **Room** para almacenar usuarios y la biblioteca de juegos.
*   **Sesión:** **DataStore (Preferences)** para almacenar el token o ID del usuario actual de forma ligera.
*   **Imágenes:** **Coil** para la carga asíncrona de carátulas.

---


