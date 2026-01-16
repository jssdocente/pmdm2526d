# Corrutinas en Kotlin

Si alguna vez has sentido que las corrutinas son complicadas, ¡no te preocupes! En realidad, son una herramienta fantástica para hacer varias cosas a la vez sin que tu aplicación se congele.

## 1. ¿Qué es una Corrutina? (La Analogía del Camarero)

Imagina un restaurante.
- **El Hilo Principal (Main Thread)** es el ÚNICO camarero que atiende las mesas.
- **Las Tareas** son los pedidos de los clientes.

### Sin Corrutinas (Bloqueo)
Si un cliente pide un café y el camarero se va a la cocina, se queda allí parado mirando la cafetera hasta que el café está listo (5 minutos). Durante esos 5 minutos, **nadie más es atendido**. Los nuevos clientes se enfadan (la app se congela y sale el aviso de "La aplicación no responde").

```kotlin title="Pseudocódigo Bloqueante (Malo)"
fun main() {
    atenderMesa1() // Llega cliente mesa 1
    // ... esperando ...
    // ¡EL CAMARERO SE QUEDA PARADO EN LA COCINA! (Bloqueo)
    // ... esperando ...
    // Mesa 2 intenta llamar al camarero pero nadie responde (ANR)
    servirMesa1()
}
```

### Con Corrutinas (No bloqueo)
El camarero toma el pedido del café, lo pasa a la cocina (lanza una corrutina) y **vuelve inmediatamente** a atender a otras mesas. Cuando la cocina avisa "¡Café listo!", el camarero lo recoge y lo sirve.
¡El camarero siempre está disponible!

```kotlin title="Pseudocódigo con Corrutinas (Bueno)"
fun main() {
    launch { // "Lanzamos" la tarea en segundo plano
        tomarPedidoMesa1()
        suspend esperarCocina() // ¡PAUSA! El camarero se va a hacer otras cosas
        // ... (5 minutos después) ...
        // La cocina avisa, el camarero vuelve y retoma aquí
        servirMesa1()
    }

    atenderMesa2() // ¡El camarero puede atender a la mesa 2 inmediatamente!
}
```

---

## 2. Corrutinas vs Hilos (Threads)

A veces se confunden, pero hay una gran diferencia: el **peso**.

- **Hilos (Threads)**: Son **pesados**. Cada hilo consume mucha memoria RAM del sistema. Crear uno nuevo es costoso. Si intentas crear 100.000 hilos, tu ordenador probablemente colapsará (Out of Memory).
- **Corrutinas**: Son **ligérisimas**. Puedes crear 100.000 corrutinas y a tu ordenador ni le importará. Son como "hilos virtuales" que viven dentro de los hilos reales.

> **Resumen**: Las corrutinas son mucho más eficientes y baratas que los hilos tradicionales.

---

## 3. Funciones de suspensión (`suspend fun`)

Una función `suspend` es una función con un superpoder: **puede pausarse y reanudarse**.

Cuando llamas a una función `suspend` (como `delay(1000)` o una petición a internet), la corrutina se "pausa" sin bloquear al hilo principal. El hilo queda libre para hacer otras cosas (pintar la pantalla, responder a clics). Cuando la operación termina, la corrutina se "despierta" y continúa donde se quedó.

```kotlin
suspend fun pedirDatosAInternet(): String {
    delay(2000) // Simula espera de 2 segundos. ¡No bloquea el hilo!
    return "Datos recibidos"
}
```

---

## 4. Builders: ¿Cómo arranco una corrutina?

Hay dos formas principales de iniciar una corrutina:

### A. `launch`: "Lanzar y olvidar"
Lo usas cuando quieres hacer algo y **no necesitas que te devuelva un dato** directamente (o el resultado no afecta al flujo inmediato).
Ejemplos: Escribir un log en un archivo, mandar una analítica, guardar una preferencia.

```kotlin
scope.launch {
    guardarEnBaseDeDatos(usuario)
    println("Usuario guardado")
}
```

### B. `async`: "Pedir y esperar resultado"
Lo usas cuando **necesitas un valor de vuelta**. `async` devuelve un `Deferred` (una promesa de que tendrás un dato en el futuro). Usas `.await()` para obtener ese dato.

```kotlin
scope.launch {
    // Pedimos dos cosas a la vez (paralelo)
    val climaDeferred = async { obtenerClima() }
    val noticiasDeferred = async { obtenerNoticias() }

    // Esperamos a tener ambos
    val clima = climaDeferred.await()
    val noticias = noticiasDeferred.await()
    
    mostrarEnPantalla(clima, noticias)
}
```

---

## 5. Dispatchers: ¿DÓNDE se ejecuta el trabajo?

Es crucial elegir el `Dispatcher` (Despachador) correcto para no congelar la app. Imagina que son "carriles" o "departamentos".

| Dispatcher | Uso Correcto | Ejemplo |
| :--- | :--- | :--- |
| **Dispatchers.Main** | **Interfaz de Usuario (UI)**. Solo operaciones rápidas y ligeras. | Actualizar un TextView, mostrar un Toast, animaciones. |
| **Dispatchers.IO** | **Entrada/Salida (Input/Output)**. Operaciones largas que esperan datos. | Leer/Escribir archivos, Bases de Datos (Room), Peticiones de Red (Retrofit). |
| **Dispatchers.Default** | **Procesamiento CPU**. Operaciones complejas de cálculo. | Procesar una imagen grande, ordenar una lista gigante, calcular algoritmos complejos. |

**Ejemplo de cambio de contexto (`withContext`):**

```kotlin
scope.launch(Dispatchers.Main) { // 1. Empezamos en el Hilo Principal (UI)
    mostrarCargando() 
    
    val datos = withContext(Dispatchers.IO) { // 2. Nos mudamos al hilo IO para lo pesado
        descargarDatosDeInternet() // Esto tarda, pero no bloquea la UI
    }
    
    ocultarCargando() // 3. Volvemos automáticamente al Main
    mostrarDatos(datos)
}
```

---

## 6. Scopes: El ciclo de vida

Las corrutinas necesitan un "dueño" o un ámbito (`Scope`) para vivir. Si el dueño muere, las corrutinas deben cancelarse para no dejar basura memoria ("zombies").

En Android, usamos los que ya nos dan hechos:

- **`lifecycleScope`** (en Activities/Fragments): Las corrutinas mueren cuando se destruye la Activity/Fragment.
- **`viewModelScope`** (en ViewModels): Las corrutinas mueren cuando se limpia el ViewModel. **(El más usado)**.

---

## 7. Ejemplos Realistas

### Caso A: Login de Usuario (ViewModel)

```kotlin
class LoginViewModel : ViewModel() {

    fun realizarLogin(user: String, pass: String) {
        // Usamos viewModelScope. Se ejecuta en Main por defecto
        viewModelScope.launch {
            try {
                mostrarSpinner(true) // UI (Main)
                
                // Nos vamos a IO para la red
                val resultado = withContext(Dispatchers.IO) {
                    apiService.login(user, pass)
                }
                
                // Volvemos a Main
                if (resultado.isExitoso) {
                    navegarAHome()
                } else {
                    mostrarError("Login incorrecto")
                }
                
            } catch (e: Exception) {
                mostrarError("Error de red: ${e.message}")
            } finally {
                mostrarSpinner(false) // UI (Main)
            }
        }
    }
}
```

### Caso B: Cargar datos de Base de Datos (Room)

```kotlin
// En un repositorio o ViewModel
fun cargarNotas() {
    viewModelScope.launch(Dispatchers.IO) { // Empezamos directo en IO
        val notas = database.notaDao().getAll()
        
        // Si necesitamos pintar esto, cambiamos a Main
        withContext(Dispatchers.Main) {
            adapter.setNotas(notas)
        }
    }
}
```

### Resumen Rápido
1.  **`suspend`**: Pausa sin bloquear.
2.  **`Main`**: Para pintar (UI).
3.  **`IO`**: Para esperar (Red/DB).
4.  **`Default`**: Para pensar (Cálculos CPU).
5.  **`viewModelScope`**: Tu mejor amigo en Android para lanzar corrutinas.

