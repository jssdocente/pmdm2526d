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
