import CodeBlock from '@theme/CodeBlock';

# Listas en Compose

En Jetpack Compose, puedes mostrar listas de elementos utilizando los componentes `LazyColumn` y `LazyRow`. Estos componentes te permiten mostrar una lista de elementos de forma eficiente y reactiva.

## LazyColumn

El componente `LazyColumn` te permite mostrar una lista de elementos de forma eficiente y reactiva. Puedes utilizar `LazyColumn` para mostrar una lista de elementos verticales que se cargan de forma perezosa a medida que el usuario se desplaza por la lista.

```kotlin
@Composable
fun ListaVertical() {
    LazyColumn {
        items(100) { index ->
            Text(text = "Elemento $index")
        }
    }
}
```

En el ejemplo anterior, se define un componente `ListaVertical` que muestra una lista de 100 elementos verticales utilizando `LazyColumn`. El método `items()` de `LazyColumn` se utiliza para generar los elementos de la lista en función de un rango de índices.

:::info[Video Listas y LazyColumn]
<iframe width="560" height="315" src="https://www.youtube.com/embed/2OUEAhX7o2g?si=zE8Xybp2x_FVVRlj" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
:::

## LazyRow

El componente `LazyRow` te permite mostrar una lista de elementos de forma eficiente y reactiva. Puedes utilizar `LazyRow` para mostrar una lista de elementos horizontales que se cargan de forma perezosa a medida que el usuario se desplaza por la lista.

```kotlin
@Composable
fun ListaHorizontal() {
    LazyRow {
        items(100) { index ->
            Text(text = "Elemento $index")
        }
    }
}
```

En el ejemplo anterior, se define un componente `ListaHorizontal` que muestra una lista de 100 elementos horizontales utilizando `LazyRow`. El método `items()` de `LazyRow` se utiliza para generar los elementos de la lista en función de un rango de índices.

## Scroll infinito

Puedes implementar el scroll infinito en las listas de Compose utilizando el método `items()` de `LazyColumn` o `LazyRow` y pasando una lista infinita como argumento. Esto te permite cargar nuevos elementos a medida que el usuario se desplaza por la lista.

```kotlin
@Composable
fun ScrollInfinito() {
    val elementos = remember { mutableStateListOf<String>() }

    LazyColumn {
        items(elementos) { elemento ->
            Text(text = elemento)
        }
    }
}
```

En el ejemplo anterior, se define un componente `ScrollInfinito` que muestra una lista de elementos utilizando `LazyColumn`. Se utiliza un estado mutable `mutableStateListOf` para almacenar los elementos de la lista, y se pasa esta lista como argumento al método `items()` de `LazyColumn`. Esto permite cargar nuevos elementos a medida que el usuario se desplaza por la lista.

El manejo del scroll infinito en Compose es similar al manejo del scroll infinito en otras bibliotecas de UI, como RecyclerView en Android. Puedes utilizar técnicas como la paginación y la carga perezosa para cargar nuevos elementos a medida que el usuario se desplaza por la lista.

El estado mutable `mutableStateListOf` se utiliza para almacenar los elementos de la lista y notificar a Compose cuando se actualizan los elementos. Esto permite que Compose vuelva a renderizar la lista con los nuevos elementos.

## Elementos personalizados

Puedes utilizar elementos personalizados en las listas de Compose para mostrar elementos más complejos. Para ello, puedes utilizar el método `item()` de `LazyColumn` o `LazyRow` y pasar un componente personalizado como argumento.

```kotlin
@Composable
fun ListaPersonalizada() {
    LazyColumn {
        items(100) { index ->
            ElementoPersonalizado(index)
        }
    }
}

@Composable
fun ElementoPersonalizado(index: Int) {
    Text(text = "Elemento $index")
}
```

En el ejemplo anterior, se define un componente `ListaPersonalizada` que muestra una lista de 100 elementos utilizando `LazyColumn`. El método `items()` de `LazyColumn` se utiliza para generar los elementos de la lista en función de un rango de índices, y se pasa un componente personalizado `ElementoPersonalizado` como argumento.

## Escuchadores de eventos

Puedes añadir escuchadores de eventos a los elementos de la lista para responder a las interacciones del usuario. Por ejemplo, puedes añadir un escuchador de clics a un elemento de la lista para realizar una acción cuando el usuario haga clic en él.

```kotlin
@Composable
fun ListaConClics() {
    LazyColumn {
        items(100) { index ->
            Text(
                text = "Elemento $index",
                modifier = Modifier.clickable { /* Acción al hacer clic */ }
            )
        }
    }
}
```

En el ejemplo anterior, se define un componente `ListaConClics` que muestra una lista de 100 elementos utilizando `LazyColumn`. Se añade un escuchador de clics al elemento de la lista utilizando el modificador `clickable`.

## Separadores

Puedes añadir separadores entre los elementos de la lista utilizando el método `item()` de `LazyColumn` o `LazyRow` y pasando un componente separador como argumento.

```kotlin
@Composable
fun ListaConSeparadores() {
    LazyColumn {
        items(100) { index ->
            Column {
                ElementoPersonalizado(index)
                Divider()
            }
        }
    }
}
```

En el ejemplo anterior, se define un componente `ListaConSeparadores` que muestra una lista de 100 elementos utilizando `LazyColumn`. Se añade un separador `Divider()` entre cada elemento de la lista.

## Filtrado y ordenación

Puedes filtrar y ordenar los elementos de la lista utilizando funciones de extensión como `filter()` y `sortedBy()`. Estas funciones te permiten realizar operaciones comunes con las listas de forma sencilla y eficiente.

```kotlin
@Composable
fun ListaFiltrada() {
    val numeros = (0..100).toList()

    LazyColumn {
        items(numeros.filter { it % 2 == 0 }) { numero ->
            Text(text = "Número $numero")
        }
    }
}
```

En el ejemplo anterior, se define un componente `ListaFiltrada` que muestra una lista de números pares del 0 al 100 utilizando `LazyColumn`. Se filtran los números pares utilizando la función de extensión `filter()`.

## Porqué usar LazyColumn y LazyRow en lugar de Column y Row

`LazyColumn` y `LazyRow` son componentes optimizados para mostrar listas de elementos de forma eficiente y reactiva. A diferencia de `Column` y `Row`, que renderizan todos los elementos de la lista de forma inmediata, `LazyColumn` y `LazyRow` renderizan solo los elementos visibles en la pantalla y los elementos que están cerca de la zona visible.

Esto hace que `LazyColumn` y `LazyRow` sean más eficientes en términos de rendimiento y consumo de recursos, especialmente cuando se trabaja con listas grandes o infinitas.

Por lo tanto, es recomendable utilizar `LazyColumn` y `LazyRow` para mostrar listas de elementos en Jetpack Compose, ya que proporcionan una experiencia de usuario más fluida y eficiente.

## Cuadrículsa o Grids

En Jetpack Compose, puedes mostrar listas de elementos en forma de cuadrículas utilizando el componente `LazyVerticalGrid` o `LazyHorizontalGrid`. Estos componentes te permiten mostrar una cuadrícula de elementos de forma eficiente y reactiva.

```kotlin
@Composable
fun CuadriculaVertical() {
    LazyVerticalGrid(cells = GridCells.Fixed(3)) {
        items(100) { index ->
            Text(text = "Elemento $index")
        }
    }
}
```

En el ejemplo anterior, se define un componente `CuadriculaVertical` que muestra una cuadrícula de 100 elementos en 3 columnas utilizando `LazyVerticalGrid`. El método `items()` de `LazyVerticalGrid` se utiliza para generar los elementos de la cuadrícula en función de un rango de índices.

```kotlin
@Composable
fun CuadriculaHorizontal() {
    LazyHorizontalGrid(cells = GridCells.Fixed(3)) {
        items(100) { index ->
            Text(text = "Elemento $index")
        }
    }
}
```

En el ejemplo anterior, se define un componente `CuadriculaHorizontal` que muestra una cuadrícula de 100 elementos en 3 filas utilizando `LazyHorizontalGrid`. El método `items()` de `LazyHorizontalGrid` se utiliza para generar los elementos de la cuadrícula en función de un rango de índices.

