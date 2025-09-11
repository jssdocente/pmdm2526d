# Capa de UI

En esta sección vamos a ver cómo implementar la capa de UI en una arquitectura MVVM (Model-View-ViewModel) en una aplicación Android con Jetpack Compose.

## ¿Qué es MVVM?

MVVM es un patrón de arquitectura de software que se utiliza para separar la lógica de presentación de la lógica de negocio en una aplicación. En MVVM, la capa de UI se divide en tres componentes principales:

- **Model**: Representa los datos y la lógica de negocio de la aplicación.

- **View**: Representa la interfaz de usuario de la aplicación.

- **ViewModel**: Actúa como un intermediario entre el Model y la View. Se encarga de manejar la lógica de presentación y de exponer los datos necesarios para que la View pueda mostrarlos. 

MVVM es un patrón muy utilizado en el desarrollo de aplicaciones Android, ya que facilita la separación de responsabilidades y la reutilización de código.  

## Implementación de la capa de UI en MVVM

Para implementar la capa de UI en una arquitectura MVVM en una aplicación Android con Jetpack Compose, puedes seguir los siguientes pasos:  

1. **Definir el Model**: Define las clases y estructuras de datos que representan los datos y la lógica de negocio de tu aplicación. Por ejemplo, puedes definir una clase `User` que represente un usuario de la aplicación.

2. **Definir el ViewModel**: Define una clase que extienda `ViewModel` y que contenga la lógica de presentación de tu aplicación. Por ejemplo, puedes definir un ViewModel que contenga la lógica para cargar los datos de un usuario.

3. **Definir la View**: Define la interfaz de usuario de tu aplicación utilizando Jetpack Compose. Por ejemplo, puedes definir una función componible que muestre los datos de un usuario en la pantalla.

4. **Conectar el ViewModel con la View**: Conecta el ViewModel con la View para que la View pueda mostrar los datos del ViewModel. Puedes utilizar `rememberViewModel` para crear una instancia del ViewModel en la View y `viewModel` para acceder a los datos del ViewModel.

## Ejemplo de implementación

!!! info "Video explicación de la arquitectura MVVM"

    <iframe width="560" height="315" src="https://www.youtube.com/embed/Af72QxiHRh0?si=RtYClOSgTyUT39dQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>



## Recursos

- [Documentación oficial de Jetpack Compose](https://developer.android.com/jetpack/compose?hl=es-419): La documentación oficial de Jetpack Compose, que incluye guías, tutoriales y ejemplos para aprender a usar Compose.

- [Navegación y arquitectura de la app (MVVM) - Codelabs](https://developer.android.com/courses/android-basics-compose/unit-4?hl=es-419)

<!-- - Ejercicio Lista de la compra - [Enunciado](https://github.com/resuadam2/enunciados-ejemplos-android-24?tab=readme-ov-file#app-de-lista-de-la-compra-o-to-do-list) - [Solución](https://github.com/resuadam2/ListaCompraCompose)

- App de Carta Alta - [Enunciado](https://github.com/resuadam2/enunciados-ejemplos-android-24/blob/master/CartaAlta/CartaAlta.md) - [Solución](https://github.com/resuadam2/CartaAltaApp) -->

 
