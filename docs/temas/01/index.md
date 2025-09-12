# Evolución y Entornos de desarrollo


## 1. Limitaciones en el desarrollo móvil
---------------------------------------------------------------

A menudo, cuando empezamos a programar, venimos de un mundo de desarrollo de escritorio o incluso web, donde los recursos parecen casi infinitos. Un ordenador de sobremesa moderno tiene gigabytes de RAM, procesadores de múltiples núcleos a altas velocidades, almacenamiento masivo y una conexión a internet estable y rápida. Los servidores que alojan aplicaciones web son aún más potentes.

Sin embargo, el entorno móvil es un ecosistema completamente diferente. Un smartphone, por muy avanzado que sea, es un dispositivo que llevamos en el bolsillo, alimentado por una batería y sujeto a condiciones muy variables. Ignorar estas limitaciones no solo lleva a una mala calificación en esta asignatura, sino, lo que es peor, a crear aplicaciones que los usuarios desinstalarán por ser lentas, consumir su batería o no funcionar cuando más las necesitan.

Pensemos en un desarrollador de escritorio como el arquitecto de un gran rascacielos con cimientos profundos y acceso a la red eléctrica principal. En cambio, un desarrollador móvil es como el ingeniero de un coche de Fórmula 1: cada gramo de peso, cada gota de combustible y cada pieza de la aerodinámica cuentan. La optimización no es una opción; es una obligación.

A continuación, vamos a desglosar las principales áreas de restricción.

1. **Recursos de Hardware: La eterna dieta**

      A pesar de los impresionantes avances, los dispositivos móviles operan con recursos de hardware significativamente más modestos que sus homólogos de escritorio.

      `Procesador (CPU)`: Las CPUs móviles están diseñadas con un objetivo principal: la eficiencia energética. Un procesador de escritorio puede permitirse consumir 100W o más y disipar el calor con grandes ventiladores. Una CPU móvil debe operar con un consumo mínimo para no agotar la batería en minutos y sobrecalentar el dispositivo. Esto implica velocidades de reloj más bajas y arquitecturas (como ARM) optimizadas para el bajo consumo, lo que se traduce en una menor capacidad de cómputo bruto. Tareas muy intensivas, como el renderizado de vídeo o cálculos complejos, deben ser abordadas con mucho más cuidado.

      `Memoria (RAM)`: Mientras que un PC de gama media actual puede tener 16 GB o 32 GB de RAM, un smartphone de gama alta puede tener 8 GB o 12 GB, y los de gama media, bastante menos. Además, el sistema operativo móvil (Android o iOS) es muy agresivo a la hora de gestionar esta memoria. Si tu aplicación consume demasiada RAM, el sistema no dudará en "matarla" (finalizar su proceso) sin previo aviso para liberar recursos para la aplicación que está en primer plano. Esto contrasta con un sistema de escritorio, donde las aplicaciones pueden permanecer en memoria durante días.

      `Almacenamiento`: Aunque los dispositivos modernos ofrecen más almacenamiento, sigue siendo un recurso finito y, a menudo, no ampliable. Las aplicaciones deben ser ligeras. Una aplicación de escritorio puede ocupar varios gigabytes sin que el usuario se preocupe, pero una app móvil que ocupe ese espacio será una candidata clara a ser eliminada cuando el usuario necesite liberar espacio para sus fotos o vídeos.

2. **La Batería: El recurso más preciado**

      Esta es, sin duda, la limitación más crítica y definitoria del desarrollo móvil. A diferencia del desarrollo web o de escritorio, donde la alimentación es constante, en el móvil cada ciclo de CPU, cada byte enviado por la red y cada píxel encendido en la pantalla consume una porción de un recurso muy limitado: la batería.

      `Consumo energético`: El desarrollador debe ser consciente del impacto energético de su código. Dejar un sensor activo (como el GPS) innecesariamente, realizar operaciones de red con demasiada frecuencia (polling) o ejecutar procesos complejos en segundo plano son los caminos más rápidos para agotar la batería del usuario y ganarse una reseña de una estrella en la tienda de aplicaciones.

      `Optimización del sistema`: Los sistemas operativos móviles modernos implementan mecanismos muy estrictos para controlar el consumo, como Doze Mode y App Standby en Android. Estos modos ponen las aplicaciones en un estado de "sueño profundo", restringiendo su acceso a la red y a la CPU cuando el dispositivo no está en uso. El desarrollador ya no tiene control total sobre cuándo se ejecuta su código en segundo plano.

3. **Conectividad: Un mundo inestable y costoso**

      Las aplicaciones de escritorio y web suelen asumir una conexión a internet permanente, rápida y de bajo coste (Wi-Fi o Ethernet). En el mundo móvil, la realidad es muy diferente.

      `Variabilidad de la red`: La aplicación debe funcionar de manera predecible en múltiples escenarios: una conexión Wi-Fi de alta velocidad, una red 5G, una conexión 4G inestable en un tren, una red 3G lenta en una zona rural, o incluso sin conexión alguna.

      `Latencia y ancho de banda`: La latencia en redes móviles es generalmente mayor que en redes fijas. Las transferencias de datos deben minimizarse. No puedes permitirte descargar 50 MB de datos cada vez que el usuario abre la app. Debes implementar estrategias de caché, compresión de datos y sincronización inteligente.

      `Coste de los datos`: A diferencia del Wi-Fi, los datos móviles suelen tener un coste para el usuario. Una aplicación que consuma una cantidad excesiva del plan de datos de un usuario será desinstalada rápidamente. Debes ofrecer opciones para limitar el uso de datos (por ejemplo, descargar contenido pesado solo con Wi-Fi).

4. **Fragmentación del Ecosistema**

      Mientras que en el desarrollo de escritorio se trabaja con un conjunto relativamente estándar de resoluciones de pantalla y capacidades, y en la web se usan técnicas de "responsive design", en el móvil (especialmente en Android) nos enfrentamos a una fragmentación extrema.

      `Diversidad de pantallas`: Existen miles de modelos de dispositivos con diferentes tamaños de pantalla, densidades de píxeles (DPI), y relaciones de aspecto. Tu interfaz de usuario (UI) debe adaptarse fluidamente a todas ellas, desde un teléfono pequeño hasta una tablet de gran formato.

      `Variedad de Hardware`: Más allá de la pantalla, te encontrarás con una enorme diversidad de CPUs, GPUs, cantidad de RAM y sensores disponibles (algunos tienen NFC, otros no; algunos tienen un barómetro, la mayoría no). Tu aplicación debe ser capaz de gestionar esta diversidad, ya sea adaptando su funcionalidad o informando al usuario de que una característica no está disponible en su dispositivo.

      `Versiones del Sistema Operativo`: Especialmente en Android, los usuarios tardan en actualizar sus dispositivos. No es raro tener que dar soporte a varias versiones del sistema operativo simultáneamente, cada una con sus propias APIs, características y bugs.

5. **Ciclo de Vida de la Aplicación y Restricciones del SO**
   
      En un ordenador de escritorio, el usuario lanza una aplicación y esta se ejecuta hasta que él decide cerrarla. En un dispositivo móvil, el ciclo de vida es mucho más complejo y está gestionado de forma estricta por el sistema operativo.

      `Estados de la aplicación`: Una aplicación móvil no está simplemente "abierta" o "cerrada". Pasa por múltiples estados (creada, iniciada, en primer plano, pausada, detenida, destruida). Por ejemplo, si entra una llamada mientras el usuario está usando tu app, esta pasará al estado de "pausada". Debes guardar el estado del usuario en ese momento para que, al volver, pueda continuar donde lo dejó.

      `Procesos en segundo plano (Background)`: Como mencionamos antes, la ejecución de código en segundo plano está severamente restringida. No puedes simplemente iniciar un hilo que se ejecute indefinidamente. Debes usar las APIs específicas proporcionadas por el sistema (como WorkManager en Android) que permiten al SO ejecutar tu tarea de la forma más eficiente posible (por ejemplo, agrupando tareas de varias apps para despertar al dispositivo una sola vez).


Desarrollar para dispositivos móviles es un desafío apasionante que requiere un cambio de mentalidad. No se trata de trasladar directamente las prácticas del desarrollo de escritorio o web, sino de abrazar las restricciones y convertirlas en una guía para la excelencia en la ingeniería de software.

Una aplicación móvil exitosa no es la que más funcionalidades tiene, sino la que ofrece una experiencia fluida, es respetuosa con los recursos del usuario (batería, datos, almacenamiento) y funciona de manera fiable en un entorno impredecible. Recordad siempre: en el mundo móvil, la eficiencia no es una característica, es el cimiento sobre el que se construye todo lo demás.

## 2. Desarrollo Móvil en la Actualidad: Un Ecosistema de Opciones
---------------------------------------------------------------

Hace una década, las opciones eran limitadas. Hoy, nos encontramos ante un ecosistema rico y diverso con diferentes enfoques, cada uno con sus propias filosofías, ventajas y desventajas. La elección de la tecnología es una de las decisiones más críticas que tomaréis como desarrolladores o arquitectos de software, ya que impactará directamente en el presupuesto del proyecto, el tiempo de desarrollo, el rendimiento de la aplicación y la experiencia final del usuario.

El objetivo de este documento es proporcionaros un mapa claro de este territorio. Analizaremos las tres grandes rutas que podemos tomar para construir una aplicación móvil: el desarrollo **Nativo**, el **Híbrido** y las **Progressive Web Apps (PWA)**. ¡Empecemos!

* * * * *

### A. Desarrollo Nativo: La Vía de la Máxima Potencia y Experiencia

El desarrollo nativo consiste en construir una aplicación utilizando las herramientas, lenguajes y APIs (Application Programming Interfaces) que la propia plataforma provee de forma oficial. En esencia, se crea una aplicación específica y optimizada para un único sistema operativo.

> 🔥 Esto significa que si queremos que nuestra app funcione en Android y en iOS, necesitaremos desarrollar **dos aplicaciones separadas**, una para cada plataforma, con sus respectivos códigos fuente.

!!! info "Ventajas Clave"

      -   **Rendimiento Insuperable:** La aplicación se compila a código máquina que se ejecuta directamente sobre el sistema operativo, sin capas intermedias. Esto garantiza la máxima velocidad, fluidez y capacidad de respuesta. Es la opción ideal para juegos, aplicaciones con gráficos intensivos o que realicen cálculos complejos.

      -   **Acceso Total al Hardware y APIs:** Tienes acceso inmediato y completo a todas las capacidades del dispositivo: GPS, cámara, acelerómetro, NFC, ARKit (iOS), etc. Además, eres el primero en poder usar las nuevas funcionalidades que se lanzan con cada actualización del sistema operativo.

      -   **Experiencia de Usuario (UX) Perfecta:** La interfaz de usuario (UI) se construye con los componentes nativos de la plataforma. El resultado es una aplicación que se ve y se siente exactamente como el resto del sistema operativo, lo que la hace intuitiva y familiar para el usuario.

      -   **Mayor Seguridad y Fiabilidad:** Al operar directamente sobre la plataforma, se aprovechan todas sus capas de seguridad y optimizaciones.

#### Tecnologías y Lenguajes

!!! debug "Para Android:"

    -   **Lenguajes:**

        -   **Kotlin:** Es el lenguaje moderno, conciso y seguro que Google recomienda oficialmente desde 2019. Es interoperable al 100% con Java.

        -   **Java:** Fue el lenguaje original para el desarrollo en Android. Sigue siendo muy utilizado, especialmente en proyectos más antiguos (legacy), pero Kotlin es la opción preferida para nuevos desarrollos.

    -   **Entorno de Desarrollo (IDE):** **Android Studio**. Es el IDE oficial de Google, basado en IntelliJ IDEA. Incluye todo lo necesario: editor de código, depurador, emuladores, analizadores de rendimiento, etc.

    -   **Recursos para profundizar:**

        -   [Android Developers (Oficial)](https://developer.android.com/) - El punto de partida para todo.

        -   [Documentación de Kotlin](https://kotlinlang.org/docs/home.html) - Aprende el lenguaje que impulsa el desarrollo moderno de Android.

        -   [Descargar Android Studio](https://developer.android.com/studio)

!!! note "Para iOS (Ecosistema Apple)"

    -   **Lenguajes:**

        -   **Swift:** Es el lenguaje moderno, potente y seguro creado por Apple. Es la opción recomendada para cualquier aplicación nueva en el ecosistema de Apple (iOS, iPadOS, macOS, watchOS).

        -   **Objective-C:** Es el lenguaje original de desarrollo para iOS. Aunque Swift lo ha superado en popularidad, todavía es fundamental para mantener proyectos existentes.

    -   **Entorno de Desarrollo (IDE):** **Xcode**. Es el IDE oficial de Apple, que se ejecuta exclusivamente en macOS. Proporciona todas las herramientas para desarrollar, depurar y publicar apps para todas las plataformas de Apple.

    -   **Recursos para profundizar:**

        -   [Apple Developer (Oficial)](https://developer.apple.com/) - Tu puerta de entrada al desarrollo para iOS.

        -   [Documentación de Swift](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/) - La guía oficial y completa del lenguaje Swift.

        -   [Descargar Xcode](https://developer.apple.com/xcode/)

* * * * *

### B. Desarrollo Híbrido: Un Código para Gobernarlos a Todos

El desarrollo híbrido, también conocido como multiplataforma, busca resolver el principal inconveniente del desarrollo nativo: la necesidad de mantener dos bases de código. La filosofía aquí es ***"escribe una vez, ejecuta en todas partes"*** (*write once, run anywhere*).

Se utiliza un único lenguaje y un framework específico para generar aplicaciones que funcionen tanto en Android como en iOS.

!!! info "Ventajas Clave"

    -   **Eficiencia en Coste y Tiempo:** Es la ventaja más evidente. Se necesita un solo equipo de desarrollo y un único código base, lo que reduce drásticamente los tiempos y costes de desarrollo y mantenimiento.

    -   **Lanzamiento Rápido al Mercado (Time-to-Market):** Al desarrollar para ambas plataformas simultáneamente, puedes lanzar tu producto mucho más rápido.

    -   **Consistencia de Marca:** La aplicación tendrá una apariencia muy similar en ambas plataformas, lo que puede ser beneficioso para la identidad de marca.

#### Tecnologías y Lenguajes

Existen diferentes enfoques dentro del mundo híbrido, pero los más relevantes hoy en día son:

!!! note "Flutter"

    -   **Concepto:** Es un toolkit de UI desarrollado por Google que ha ganado una tracción inmensa. Flutter no utiliza los componentes nativos de la UI, sino que trae su propio motor de renderizado (Skia) para dibujar cada píxel en la pantalla. Esto le da un control total sobre la interfaz y permite animaciones complejas a 60/120 FPS.

    -   **Lenguaje:** **Dart**. Un lenguaje moderno, orientado a objetos y optimizado para el desarrollo de UI.

    -   **Ideal para:** Aplicaciones con una interfaz de usuario muy personalizada, expresiva y con muchas animaciones.

    -   **Recursos para profundizar:**

        -   [Flutter (Oficial)](https://flutter.dev/) - Descubre por qué es una de las tecnologías más queridas por los desarrolladores.

        -   [Documentación de Dart](https://dart.dev/guides) - Aprende los fundamentos del lenguaje de Flutter.

!!! quote "React Native"

    -   **Concepto:** Creado por Meta (Facebook), React Native permite a los desarrolladores web usar sus conocimientos de JavaScript y React para crear aplicaciones móviles. A diferencia de Flutter, React Native utiliza un "puente" (bridge) para comunicarse con los componentes de la UI nativa de cada plataforma. Esto puede hacer que la app se sienta un poco más "nativa".

    -   **Lenguaje:** **JavaScript** o **TypeScript** (una versión de JavaScript con tipado estático, muy recomendada).

    -   **Ideal para:** Empresas con equipos de desarrollo web que quieran pasar al móvil, o para aplicaciones donde el aspecto nativo sea una prioridad.

    -   **Recursos para profundizar:**

        -   [React Native (Oficial)](https://reactnative.dev/) - La documentación oficial para empezar.

!!! tip ".NET MAUI (Multi-platform App UI)"

    -   **Concepto:** Es la evolución de Xamarin.Forms, impulsada por Microsoft. Permite a los desarrolladores del ecosistema .NET usar C# y XAML para crear aplicaciones para iOS, Android, Windows y macOS desde una única base de código.

    -   **Lenguaje:** **C#**.

    -   **Ideal para:** Organizaciones que ya tienen una fuerte inversión en tecnologías de Microsoft y .NET.

    -   **Recursos para profundizar:**

        -   [.NET MAUI (Oficial)](https://dotnet.microsoft.com/en-us/apps/maui) - La plataforma multiplataforma del ecosistema .NET.

* * * * *


### C. Kotlin Multiplatform (KMP): ¿Lo mejor de Ambos mundos? 

Hemos analizado el desarrollo Nativo y el Híbrido como dos caminos separados. El primero nos da el máximo rendimiento y la mejor experiencia de usuario a costa de duplicar el trabajo. El segundo nos ofrece eficiencia y una base de código única, pero a veces con compromisos en rendimiento o en acceso a las últimas funcionalidades nativas.

Pero, ¿y si existiera un enfoque que intentara combinar la eficiencia del código compartido con el poder del desarrollo nativo? Aquí es donde entra en juego **Kotlin Multiplatform (KMP)**.

KMP no es otro framework híbrido como Flutter o React Native. Es un enfoque radicalmente diferente.

**La Lógica Compartida, la UI Nativa**

La filosofía de Kotlin Multiplatform, impulsado por JetBrains (los creadores de Kotlin y de IntelliJ), no es "escribe una vez, ejecuta en todas partes", sino más bien **"escribe la lógica de negocio una vez, y construye la interfaz de usuario de forma nativa"**.

KMP permite a los desarrolladores escribir código en Kotlin que puede ser compilado para múltiples plataformas:

-   **JVM** (para aplicaciones Android y de servidor).
-   **JavaScript** (para aplicaciones web).
-   **Nativo** (utilizando la infraestructura del compilador LLVM para generar binarios para iOS, macOS, Windows, etc.).

La idea central es identificar el código que es independiente de la interfaz de usuario ---la **lógica de negocio**--- y compartirlo entre plataformas.

#### ¿Qué es la "Lógica de Negocio"?

Piensa en todo lo que tu aplicación hace "detrás de las cámaras":

-   **Acceso a la red:** Realizar llamadas a una API REST o GraphQL.
-   **Gestión de la base de datos:** Guardar, leer y actualizar datos en una base de datos local (como SQLite).
-   **Modelos de datos:** Las clases que representan la información de tu app (Usuario, Producto, etc.).
-   **Validaciones:** Comprobar que un email tiene el formato correcto o que una contraseña cumple los requisitos.
-   **Algoritmos y cálculos:** Cualquier procesamiento de datos específico de tu aplicación.

Todo este código, que suele ser el núcleo de la aplicación, no depende de si el botón en la pantalla es redondo o cuadrado. Por lo tanto, se puede escribir una sola vez en Kotlin y compartirlo.

#### ¿Y qué pasa con la Interfaz de Usuario (UI)?

Aquí es donde KMP brilla y se diferencia de los frameworks híbridos. La UI se construye de forma **100% nativa** en cada plataforma:

-   **En Android:** Creas tus vistas (layouts) y gestionas el ciclo de vida de las Activities y Fragments utilizando **Jetpack Compose** o las vistas XML tradicionales. Desde este código nativo, llamas a la lógica de negocio compartida en Kotlin.

-   **En iOS:** Desarrollas tu interfaz de usuario con **SwiftUI** o **UIKit**, exactamente como lo haría un desarrollador nativo de iOS. Desde tu código en Swift, puedes invocar directamente las funciones y clases del módulo de Kotlin compartido.

!!! info "Ventajas Clave"

    -   **Rendimiento Nativo:** Dado que la UI y toda la interacción con la plataforma se gestionan con código nativo, el rendimiento y la fluidez son idénticos a los de una aplicación puramente nativa. No hay "puentes" ni capas de abstracción que ralenticen la interfaz.

    -   **UI/UX Perfecta:** La aplicación se ve, se siente y se comporta exactamente como el usuario espera en su dispositivo, ya que utilizas los componentes nativos de Android (Material Design) y de iOS (Human Interface Guidelines).

    -   **Eficiencia sin Sacrificios:** Compartes una parte significativa del código (a menudo más del 50-60%), lo que reduce el tiempo de desarrollo, la duplicación de esfuerzos y la probabilidad de bugs, sin renunciar a las ventajas del desarrollo nativo.

    -   **Acceso Inmediato a APIs Nativas:** Como estás escribiendo código nativo en la capa de UI, puedes acceder a las últimas APIs de cada sistema operativo desde el primer día, sin esperar a que un framework de terceros añada soporte.

    -   **Adopción Gradual:** Puedes empezar a usar KMP en una pequeña parte de una aplicación existente, tanto en Android como en iOS, sin necesidad de reescribirla por completo.

!!! warning "Desventajas y Consideraciones"

    -   **Complejidad Inicial:** La configuración del proyecto puede ser más compleja que en otros enfoques. Requiere conocimientos tanto de desarrollo en Android (Gradle) como en iOS (Xcode, CocoaPods).

    -   **Se Necesitan Habilidades Nativas:** A diferencia del desarrollo híbrido puro, no basta con un solo equipo de desarrolladores. Necesitas expertos que se sientan cómodos construyendo la UI en Android (Kotlin/Compose) y en iOS (Swift/SwiftUI).

    -   **Ecosistema en Crecimiento:** Aunque está madurando rápidamente, el ecosistema de librerías multiplataforma todavía es más pequeño que el de plataformas como React Native o Flutter.

#### Un Ecosistema en Evolución: Compose Multiplatform

Para añadir una capa más, JetBrains está llevando Jetpack Compose (el moderno toolkit de UI declarativa de Android) al mundo multiplataforma con **Compose Multiplatform**.

Compose Multiplatform permite compartir no solo la lógica de negocio, sino también la **interfaz de usuario**, utilizando el mismo código declarativo en Kotlin para describir la UI en Android, iOS , escritorio (Windows, macOS, Linux) y web (Wasm).

Esto acerca a KMP al modelo de Flutter, donde tanto la lógica como la UI son compartidas, pero con la ventaja de estar construido sobre el lenguaje y el ecosistema de Kotlin.

-   **Recursos para profundizar:**

    -   [Kotlin Multiplatform (Oficial)](https://www.jetbrains.com/compose-multiplatform/) - La documentación oficial y el mejor lugar para empezar.


### D. Progressive Web Apps (PWA): La Web se Viste de App

Las PWA no son aplicaciones móviles en el sentido tradicional. Son **aplicaciones web** que utilizan las tecnologías web más modernas para ofrecer una experiencia muy similar a la de una aplicación nativa. No se instalan desde una tienda de aplicaciones, sino que el usuario puede "añadirlas a la pantalla de inicio" directamente desde el navegador.

!!! info "Ventajas Clave"

    -   **Sin Tiendas de Aplicaciones:** No necesitas pasar por los procesos de revisión y publicación de la App Store de Apple o Google Play. La "instalación" es instantánea.

    -   **Multiplataforma por Definición:** Funcionan en cualquier dispositivo que tenga un navegador web moderno (Android, iOS, Windows, macOS, etc.). El mismo código sirve para todo.

    -   **Siempre Actualizadas:** Al ser una web, el usuario siempre tiene la última versión disponible sin necesidad de actualizar nada.

    -   **Compartibles:** Se puede compartir la "app" simplemente enviando una URL.

#### Tecnologías Clave y Lenguajes

Las PWA se construyen sobre el estándar de la web.

-   **Lenguajes:** **HTML5, CSS3, JavaScript/TypeScript**.

-   **Componentes Esenciales:**

    -   **HTTPS:** Es un requisito de seguridad indispensable.

    -   **Service Worker:** Es un script que el navegador ejecuta en segundo plano. Es la tecnología que permite funcionalidades como el **trabajo offline** (acceder a la app sin conexión) y las **notificaciones push**.

    -   **Web App Manifest:** Es un archivo JSON que le dice al navegador cómo debe verse y comportarse la aplicación cuando se "instala" (nombre, icono, pantalla de bienvenida, etc.).

-   **Recursos para profundizar:**

    -   [web.dev by Google (PWA)](https://web.dev/progressive-web-apps/) - Una de las mejores guías para entender y construir PWAs.

    -   [MDN Web Docs: Service Worker API](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API) - Documentación técnica de referencia.


!!! question "¿Qué tecnología y lenguaje elegir?"

    <iframe width="560" height="315" src="https://www.youtube.com/embed//IrkJljILrzQ?t=50" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


## 3. El Ciclo de Vida de una Aplicación Móvil
---------------------------------------------------------------

Pensemos en una aplicación no como un simple programa, sino como un producto con el que un usuario interactúa a lo largo del tiempo. Este viaje, desde que oye hablar de la app hasta que decide eliminarla de su teléfono, se conoce como su ciclo de vida. Entender cada fase nos permite tomar mejores decisiones para que nuestra app no solo sea útil, sino también exitosa.

* * * * *

### A. Descubrimiento: "¿Cómo me encuentran?"

Esta es la fase de "marketing". Nuestra aplicación ya está terminada y publicada, pero para el usuario, todo empieza aquí. ¿Cómo llega un usuario a conocer nuestra existencia entre millones de aplicaciones?

-   **Tiendas de Aplicaciones (App Stores):** Son el principal escaparate.

    -   **Google Play Store (Android)** y **Apple App Store (iOS)** son los mercados dominantes. Actúan como gigantescos centros comerciales donde los usuarios pueden buscar, explorar categorías (juegos, productividad, etc.), ver listas de éxitos ("Top Ventas", "Top Gratuitas"), y leer reseñas y valoraciones de otros usuarios.

    -   **Búsqueda:** La mayoría de los descubrimientos ocurren a través del buscador de la tienda. Por eso, un buen nombre, un icono atractivo y una descripción clara (lo que se conoce como **ASO - App Store Optimization**) son cruciales.

    -   **Recomendaciones Editoriales:** Ser destacado por los equipos editoriales de Google o Apple puede catapultar una aplicación a la fama.

-   **Otros Canales:** No todo ocurre en las tiendas.

    -   **Buscadores Web:** Una búsqueda en Google puede llevar a la ficha de una aplicación en la tienda.

    -   **Redes Sociales y "Boca a Boca":** Recomendaciones de amigos, influencers o publicidad en plataformas como Instagram, TikTok o X.

    -   **Medios de Comunicación:** Artículos en blogs de tecnología o noticias que hablen de nuestra app.

    -   **Publicidad Directa:** Un código QR en un cartel o un enlace en una web pueden llevar directamente a la página de instalación.

* * * * *

### B. Instalación: "Te quiero en mi móvil"

Una vez descubierta la app, el usuario decide "adquirirla". Este proceso, aunque parece simple, tiene matices importantes, sobre todo en cuanto a seguridad.

-   **Desde Tiendas Oficiales:**

    -   Este es el método **seguro y recomendado**.

    -   El usuario pulsa el botón "Instalar" (en Google Play) o "Obtener" (en la App Store).

    -   La tienda gestiona todo el proceso de forma segura: verifica la identidad del usuario, descarga el paquete de la aplicación (`.apk` en Android, `.ipa` en iOS) y lo instala en el dispositivo. El sistema operativo coloca el icono de la app en la pantalla de inicio o en el cajón de aplicaciones.

-   **Desde Fuentes Externas ("Sideloading"):**

    -   Esta opción es prácticamente **exclusiva de Android**. Permite a los usuarios instalar aplicaciones descargando el archivo `.apk` directamente desde una página web o una tienda de aplicaciones alternativa (como F-Droid, que se especializa en software de código abierto).

    -   **Riesgos de Seguridad:** El "sideloading" es la principal vía de entrada de **malware** en Android. La aplicación no ha pasado los controles de seguridad de Google Play, por lo que podría contener software malicioso.

    -   Por defecto, Android bloquea estas instalaciones. El usuario debe conceder explícitamente permiso a la aplicación (por ejemplo, al navegador Chrome) para "instalar aplicaciones desconocidas", asumiendo el riesgo que conlleva.

* * * * *

### C. Ejecución: "¡A funcionar!"

Una vez instalada, la aplicación está lista para ser utilizada. Al pulsar su icono por primera vez, ocurren varias cosas:

1.  **Carga en Memoria:** El sistema operativo carga el código de la aplicación en la memoria RAM del dispositivo.

2.  **Pantalla de Bienvenida (Splash Screen):** A menudo se muestra una pantalla de inicio con el logo de la app mientras se cargan los recursos necesarios en segundo plano.

3.  **Solicitud de Permisos:** ¡Un paso fundamental! Las aplicaciones ya no reciben todos los permisos al instalarse. Ahora, deben solicitar acceso a funciones sensibles **en tiempo de ejecución**, es decir, la primera vez que necesitan usarlas.

    -   **Ejemplo:** Una app de mensajería pedirá acceso a tus contactos cuando intentes buscar a un amigo, y pedirá acceso a la cámara cuando pulses el botón para hacer una foto.

    -   El usuario puede **Aceptar** o **Denegar** cada permiso individualmente. Como desarrolladores, debemos gestionar qué ocurre si un usuario deniega un permiso esencial.

Una vez configurada y con los permisos necesarios, la aplicación se encuentra en su estado normal de uso, interactuando con el usuario.

* * * * *

### D. Actualización: "Mejorando contigo"

Una aplicación no es un producto estático. El software necesita evolucionar para seguir siendo útil y seguro.

-   **Motivos para una Actualización:**

    -   **Nuevas Funcionalidades:** Añadir características que los usuarios han pedido o que mejoran el producto.

    -   **Corrección de Errores (Bugs):** Ningún software es perfecto. Las actualizaciones solucionan fallos y problemas de estabilidad.

    -   **Mejoras de Rendimiento:** Optimizar el código para que la app sea más rápida o consuma menos batería.

    -   **Parches de Seguridad:** Solucionar vulnerabilidades que podrían poner en riesgo los datos del usuario.

-   **Proceso de Actualización:**

    -   **Automáticas:** Es el método más común. Las tiendas de aplicaciones descargan e instalan las nuevas versiones en segundo plano, generalmente cuando el dispositivo está conectado a una red Wi-Fi y cargando, para no molestar al usuario.

    -   **Manuales:** El usuario puede ir a la sección "Mis aplicaciones" de la tienda y forzar la actualización de una o todas las apps pendientes.

* * * * *

### E. Borrado (Desinstalación): "Ha sido un placer"

Llega un momento en que el usuario ya no necesita la aplicación. El proceso de desinstalación está diseñado para ser simple, pero es importante saber qué se elimina y qué puede quedar.

-   **Proceso Estándar:** El usuario realiza una pulsación larga sobre el icono de la app y selecciona la opción "Desinstalar" o "Eliminar App".

-   **¿Desinstalación Completa o Parcial?**

    -   Generalmente, la desinstalación que realiza el usuario es **completa** desde el punto de vista de la aplicación en sí. El sistema operativo elimina:

        1.  El **paquete de la aplicación** (`.apk` / `.ipa`).

        2.  El **almacenamiento privado** de la app (su "sandbox"). Aquí se guardan las configuraciones, bases de datos internas, caché y otros archivos que la app necesita para funcionar.

    -   **¿Qué puede quedar (Datos Residuales)?** A veces, la desinstalación no elimina el 100% de los datos asociados. Esto no es una "desinstalación parcial" que el usuario elige, sino una consecuencia de cómo funcionan los sistemas de archivos.

        -   **Archivos en Almacenamiento Compartido:** Si tu aplicación de edición de fotos guardó una imagen en la carpeta `DCIM/MisFotos`, ese archivo **no se borrará** al desinstalar la app. El sistema lo considera propiedad del usuario, no de la aplicación.

        -   **Datos en la Nube:** Si el usuario creó una cuenta en tus servidores, esa cuenta y sus datos asociados (perfil, historial, etc.) **permanecen en la nube**. No se eliminan al borrar la app del teléfono. El usuario tendría que eliminar su cuenta explícitamente.


### Comparativa de Ciclos de Vida -- Web vs PWA vs Nativa

| Fase Ciclo Vida         | 🌐 Web Tradicional                 | 🚀 Progressive Web App (PWA)    | 📱 Aplicación  |
|------------------------|-----------------------------------|---------------------------------|----------------|
1. **Descubrimiento** |	**Máxima visibilidad**. A través de buscadores (Google, etc.), enlaces, redes sociales. No depende de una tienda. |	Lo mejor de ambos mundos. Descubrible como una web (buscadores, enlaces) y potencialmente listada en tiendas de apps (ej. Google Play). |Dependiente de la tienda. Principalmente a través de la App Store y Google Play. El ASO es crucial. |
2. **Instalación**	| **Sin instalación**. La principal ventaja. El usuario accede al instante. Cero fricción. | Instalación opcional y ligera. El usuario puede "Añadir a la pantalla de inicio". Es un proceso casi instantáneo y no ocupa mucho espacio. | Instalación obligatoria. El usuario debe ir a la tienda, descargar varios MB (o GB) y esperar a que se instale. Es el punto de mayor fricción. |
3.  **Ejecución** |	**Dentro del navegador**. Se ejecuta en una pestaña, con las limitaciones de la interfaz del navegador (barra de URL, etc.). Requiere conexión.| Como una app. Se lanza desde su propio icono en la pantalla de inicio, a pantalla completa. Puede funcionar offline gracias al Service Worker.	| Directamente en el SO. Se lanza desde su icono. Ofrece la máxima integración, rendimiento y acceso completo al hardware del dispositivo (cámara, GPS, etc.).|
4. **Actualización**	| **Transparente e instantánea**. Cada vez que el usuario entra, recibe la última versión del servidor. No hay proceso de actualización.|	Automática y en segundo plano. El Service Worker busca y actualiza la app de forma silenciosa. El usuario tiene la nueva versión la próxima vez que la abre.|	Gestionada por la tienda. Puede ser automática o manual. Requiere descargar de nuevo el paquete completo o una parte, y un proceso de instalación.|
5. **Borrado** |	**No existe**. El usuario simplemente cierra la pestaña o borra el historial/caché del navegador. |	Sencillo. El usuario elimina el icono de la pantalla de inicio, igual que en una app nativa. Los datos se pueden borrar desde la configuración del navegador. |	Proceso manual. El usuario debe realizar una desinstalación explícita (pulsación larga, etc.) para liberar el espacio de almacenamiento. |


⚡ **Análisis Detallado de las Diferencias**

##### Fricción en la Entrada: La Gran Diferencia

La principal ventaja de la web y las PWAs es la **inmediatez**. Piensa en cuántas veces has entrado a una web que no conocías frente a cuántas apps nuevas has instalado en el último mes. El proceso de ir a una tienda, esperar la descarga y la instalación es una barrera (**fricción**) que hace que muchos usuarios abandonen el proceso. Una PWA elimina casi por completo esa barrera, ofreciendo una experiencia similar a la nativa con la facilidad de acceso de una web.

##### Capacidades y Acceso al Dispositivo

Aquí es donde la **aplicación nativa sigue siendo la reina**.

-   **Web:** Está limitada por el "sandbox" (caja de arena) del navegador. Tiene acceso limitado a sensores y hardware.

-   **PWA:** Mejora mucho respecto a la web. Gracias a las nuevas APIs, puede acceder a notificaciones push, ubicación, cámara y funcionamiento offline. Sin embargo, todavía tiene un acceso más restringido que una app nativa, especialmente en iOS.

-   **Nativa:** Tiene acceso total y de máximo rendimiento a todas las capacidades del dispositivo: NFC, Bluetooth avanzado, sensores, archivos del sistema, etc.

##### El Proceso de Actualización: Control vs. Inmediatez

-   El modelo **nativo** da al usuario (y al desarrollador) un mayor control sobre las versiones. A veces, los usuarios deciden no actualizar una app si no les gusta la nueva versión.

-   El modelo **web/PWA** es mucho más ágil. Como desarrollador, te aseguras de que todos tus usuarios están utilizando siempre la última versión, lo que simplifica enormemente el mantenimiento y la corrección de errores. No tienes que dar soporte a versiones antiguas.


No hay una tecnología superior a las otras en todos los aspectos. La elección depende enteramente del **propósito del proyecto**.

-   Para una herramienta que necesita el **máximo rendimiento** y una integración profunda con el hardware (un juego, una app de edición de vídeo, una app que use Bluetooth intensivamente), el camino **nativo** es indiscutible.

-   Para un blog, una tienda online o una herramienta de consulta donde la **inmediatez y el alcance** son lo más importante, una **PWA** es una solución fantástica, ya que combina la visibilidad de la web con una experiencia de usuario muy mejorada.

-   Una **web tradicional** sigue siendo perfecta para contenido informativo simple o sitios donde la funcionalidad offline y las notificaciones no aportan un valor añadido significativo.
  


## Android: El SDK de Android y sus Versiones
---------------------------------

En este apartado vamos a desmontar la "caja de herramientas" que nos permite construir aplicaciones para Android. Hablaremos del **SDK**, de sus componentes y de cómo entender el sistema de versiones de Android, que es crucial para garantizar que nuestras apps funcionen correctamente en la mayoría de dispositivos posibles.

Pensemos en nosotros como chefs que quieren preparar un plato específico (nuestra app). El SDK sería nuestra cocina profesional: no solo nos da los ingredientes (las APIs), sino también los cuchillos, los fogones y los manuales de recetas (las herramientas y la documentación). Sin esta cocina, solo tendríamos ideas, pero no podríamos cocinar nada.

* * * * *

#### A. ¿Qué es un SDK (Software Development Kit)?

Un **SDK**, o Kit de Desarrollo de Software, es un conjunto de herramientas de software y programas proporcionados por un fabricante de hardware o software para permitir la creación de aplicaciones para una plataforma específica. En nuestro caso, Google nos proporciona el **SDK de Android** para que podamos crear apps para su sistema operativo.

Un SDK típicamente incluye:

-   **Bibliotecas de código (APIs):** Código preescrito que nos da acceso a las funcionalidades del dispositivo, como la cámara, el GPS o la interfaz de usuario.

-   **Depurador (Debugger):** Una herramienta para encontrar y corregir errores en nuestro código.

-   **Documentación:** Manuales, guías y ejemplos que nos enseñan a usar las herramientas y las bibliotecas.

-   **Emuladores:** Programas que nos permiten probar nuestra app en un dispositivo virtual sin necesidad de tener un teléfono físico.

En resumen, **el SDK es el paquete TODO-EN-UNO indispensable para empezar a desarrollar**.

* * * * *

### B. Los Componentes del SDK de Android

Dentro de Android Studio, el SDK se gestiona a través del "SDK Manager" y se divide principalmente en dos pestañas: **SDK Platforms** y **SDK Tools**. Es vital entender la diferencia.

#### **SDK Platforms**

Piensa en una "Platform" como una **versión específica y completa del sistema operativo Android empaquetada para el desarrollador**. Cada "Platform" corresponde a un nivel de API concreto (que veremos más adelante).

Cada paquete de "SDK Platform" incluye:

-   El **`android.jar`**: Este es el archivo clave. Contiene todas las APIs y clases de esa versión de Android contra las que compilaremos nuestro código. Es nuestro "diccionario" de funcionalidades disponibles.

-   **Imagen del Sistema (System Image):** Es una copia del sistema operativo Android que se usa para ejecutar los emuladores. Si quieres probar tu app en un emulador de Android 14, necesitas descargar la "System Image" de Android 14.

**En resumen:** Si quieres que tu app use funciones de Android 14 y probarla en un emulador de Android 14, **necesitas descargar la "SDK Platform" para el nivel de API de Android 14.**

#### **SDK Tools**

Estas son las herramientas **independientes de la versión de Android** que usamos para desarrollar, depurar y probar nuestra aplicación. Son la maquinaria de nuestra cocina. No importa si cocinamos una receta de 2020 o de 2024, los fogones y cuchillos son los mismos (aunque se afilen y mejoren de vez en cuando).

Las herramientas más importantes aquí son:

-   **Android SDK Build-Tools:** Un conjunto de herramientas que toman nuestro código y lo compilan y empaquetan en un archivo `.apk` o `.aab` instalable.

-   **Android Emulator:** El software que nos permite crear y ejecutar dispositivos virtuales.

-   **Android SDK Platform-Tools:** Incluye herramientas de línea de comandos esenciales como:

    -   **ADB (Android Debug Bridge):** El "puente" de comunicación entre nuestro ordenador y el dispositivo (físico o emulado). Nos permite instalar apps, depurar, ver logs, etc.

    -   **Fastboot:** Para flashear el firmware del dispositivo.

**En resumen:** Las **SDK Tools** son las herramientas generales para construir y probar, mientras que las **SDK Platforms** son los "ingredientes" específicos de cada versión de Android.

* * * * *

### C. Entendiendo las Versiones de Android: ¡La Clave está en el Nivel de API!

Aquí es donde muchos estudiantes se lían. Google usa dos nombres para cada versión de Android, y es crucial saber cuál es el importante para nosotros, los desarrolladores.

#### **Nombre Comercial (y postre 🍰)**

Es el nombre público y de marketing que Google utiliza para cada gran lanzamiento. Suelen ser nombres de postres o dulces en orden alfabético (aunque esta tradición se ha relajado en los últimos años).

-   **Ejemplos:** Android 8 (**O**reo), Android 9 (**P**ie), Android 10, Android 11, Android 12 (**S**now Cone).

Este nombre es **útil para los usuarios**, pero **casi irrelevante para los desarrolladores**. Es una etiqueta de marketing.

#### **Nivel de API (API Level)**

Este es el **número que realmente nos importa**. El Nivel de API es un **único número entero** que identifica de forma inequívoca la versión del framework de APIs que ofrece una plataforma Android.

Cuando se lanzan nuevas funcionalidades para desarrolladores (por ejemplo, un nuevo tipo de notificación o un permiso de privacidad), Google incrementa el Nivel de API.

**¿Por qué es tan importante?** Porque nuestro código se escribe "contra" un nivel de API. Si usamos una función que se introdujo en el **API Level 33 (Android 13)**, nuestra aplicación **no funcionará** en un dispositivo con **API Level 32 (Android 12L)**, porque esa función simplemente no existe en ese sistema operativo. ¡La app "crasheará"!


### D. Listado de Versiones de Android

Las versiones de Android incluyen nombres de dulces y postres como Cupcake, Donut, Eclair, Froyo, Gingerbread, Honeycomb, Ice Cream Sandwich, Jelly Bean, KitKat, Lollipop, Marshmallow, Nougat, Oreo y Pie. Posteriormente, a partir de Android 10, se han numerado las versiones (Android 10, 11, 12, 13, 14, 15, etc.). 

Versiones antiguas (con nombres de postres) 

-   **Android 1.5** - Cupcake
-   **Android 1.6** - Donut
-   **Android 2.0/2.1** - Eclair
-   **Android 2.2** - Froyo
-   **Android 2.3** - Gingerbread
-   **Android 3.0** - Honeycomb
-   **Android 4.0** - Ice Cream Sandwich
-   **Android 4.1 - 4.3** - Jelly Bean
-   **Android 4.4** - KitKat
-   **Android 5.0/5.1** - Lollipop
-   **Android 6.0** - Marshmallow
-   **Android 7.0/7.1** - Nougat
-   **Android 8.0/8.1** - Oreo
-   **Android 9.0** - Pie

Versiones recientes (numeradas) 

-   **Android 10** - Fue la primera versión sin nombre de postre.
-   **Android 11** - Lanzada en 2020.
-   **Android 12** - Lanzada en 2021, con una variante optimizada para pantallas grandes llamada Android 12L.
-   **Android 13** - Lanzada en 2022.
-   **Android 14** - Lanzada en 2023.
-   **Android 15** - Lanzada en 2024.
-   **Android 16** - Lanzada en 2025.

Cómo verificar la versión de Android en tu dispositivo 

-   Abre la aplicación Configuración o Ajustes.
-   Desplázate hacia abajo y selecciona Acerca del teléfono o Acerca de la tablet.
-   Busca la opción Versión de Android para ver la información de tu sistema operativo.

ℹ️ Más información en: [Lista de versiones de Android](https://developer.android.com/about/versions?hl=es-419)