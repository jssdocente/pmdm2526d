# **Corrutinas en Unity**

Vamos a ver qué son las corrutinas en Unity y cómo funcionan. Las corrutinas son una forma de ejecutar código de manera asíncrona, lo que significa que puedes pausar la ejecución de una función y reanudarla más tarde sin bloquear el hilo principal del juego.
Esto es útil para tareas que requieren esperar un tiempo determinado, como animaciones, temporizadores o esperas entre acciones.

## **¿Qué son las corrutinas?**

Las corrutinas son funciones que pueden pausar su ejecución y reanudarla en un momento posterior. En Unity, las corrutinas se implementan utilizando el tipo de retorno `IEnumerator`. Esto permite que la función devuelva un valor y pause su ejecución en cualquier momento utilizando la palabra clave `yield`. Cuando se llama a una corrutina, se devuelve un objeto `Coroutine` que representa la ejecución de la función.   

## **¿Cómo funcionan?**

Para crear una corrutina, debes definir una función que devuelva un `IEnumerator`. Dentro de la función, puedes usar la palabra clave `yield` para pausar la ejecución y devolver un valor. Por ejemplo, puedes usar `yield return null` para pausar la ejecución hasta el siguiente fotograma o `yield return new WaitForSeconds(2)` para pausar la ejecución durante 2 segundos.
Aquí tienes un ejemplo de una corrutina que espera 2 segundos antes de imprimir un mensaje en la consola:

```csharp
using System.Collections;
using UnityEngine;
using UnityEngine.UI;
using TMPro;
using UnityEngine.SceneManagement;
using UnityEngine.EventSystems;

public class CorrutinaEjemplo : MonoBehaviour
{
    // Método que inicia la corrutina
    public void IniciarCorrutina()
    {
        StartCoroutine(MiCorrutina());
    }

    // Corrutina que espera 2 segundos antes de imprimir un mensaje
    private IEnumerator MiCorrutina()
    {
        Debug.Log("Esperando 2 segundos...");
        yield return new WaitForSeconds(2);
        Debug.Log("¡2 segundos han pasado!");
    }
}
```

En este ejemplo, al llamar al método `IniciarCorrutina`, se inicia la corrutina `MiCorrutina`, que espera 2 segundos antes de imprimir el mensaje "¡2 segundos han pasado!" en la consola.  

## **Ejemplo práctico**

Vamos a crear un ejemplo práctico de una corrutina que hace que un objeto se mueva de un punto a otro en la escena. Para ello, crearemos un script que moverá un objeto de su posición actual a una nueva posición en 2 segundos.

```csharp
using System.Collections;
using UnityEngine;
using UnityEngine.UI;

public class MoverObjeto : MonoBehaviour
{
    public Transform objetivo; // El objeto al que queremos movernos
    public float tiempoMovimiento = 2f; // Tiempo que tardará en moverse

    // Método que inicia la corrutina
    public void Mover()
    {
        StartCoroutine(MoverAObjetivo());
    }

    // Corrutina que mueve el objeto a la posición del objetivo
    private IEnumerator MoverAObjetivo()
    {
        Vector3 posicionInicial = transform.position;
        Vector3 posicionFinal = objetivo.position;
        float tiempoTranscurrido = 0f;

        while (tiempoTranscurrido < tiempoMovimiento)
        {
            transform.position = Vector3.Lerp(posicionInicial, posicionFinal, tiempoTranscurrido / tiempoMovimiento);
            tiempoTranscurrido += Time.deltaTime;
            yield return null; // Espera hasta el siguiente fotograma
        }

        transform.position = posicionFinal; // Asegúrate de que el objeto llegue a la posición final
    }
}
```

En este ejemplo, al llamar al método `Mover`, se inicia la corrutina `MoverAObjetivo`, que mueve el objeto de su posición actual a la posición del objeto `objetivo` en 2 segundos. La función `Vector3.Lerp` se utiliza para interpolar entre las dos posiciones a lo largo del tiempo.

## **Las corrutinas en Unity 6**

Las corrutinas clásicas (IEnumerator) siguen funcionando igual, por lo que a pesar de que Unity 6 ha introducido el nuevo sistema de tareas, las corrutinas clásicas siguen siendo una opción válida y útil para muchas situaciones. Sin embargo, el nuevo sistema de tareas ofrece una forma más moderna y flexible de manejar la asincronía en Unity.

Las Nuevas APIs con **UnityEngine.Awaitable y **async/await** xcon su propio sistema de corrutinas basadas en C# moderno es soportado por Unity 6. Es completamente opcional pero si que es algo más avanzado que el uso de IEnumerator.

Vamos a ver un ejemplo que agrega un comportamiento visual cuando un jugador recibe daño usando una corrutina que hace que parpadee el sprite del jugador.

- Con IEnumerator:

```csharp title="DamageEffect.cs"
using System.Collections;
using UnityEngine;

public class DamageEffect : MonoBehaviour
{
    private SpriteRenderer sr;
    public float flashDuration = 0.1f;
    public int flashCount = 5;

    void Awake()
    {
        sr = GetComponent<SpriteRenderer>();
    }

    public void TriggerFlash()
    {
        StartCoroutine(FlashRoutine());
    }

    private IEnumerator FlashRoutine()
    {
        for (int i = 0; i < flashCount; i++)
        {
            sr.color = new Color(1, 1, 1, 0); // Transparente
            yield return new WaitForSeconds(flashDuration);
            sr.color = new Color(1, 1, 1, 1); // Visible
            yield return new WaitForSeconds(flashDuration);
        }
    }
}
```

- Con async/await:

```csharp title="DamageEffect.cs"
using UnityEngine;
using System.Threading.Tasks;
using UnityEngine.Awaitable;

public class DamageEffectAsync : MonoBehaviour
{
    private SpriteRenderer sr;
    public float flashDuration = 0.1f;
    public int flashCount = 5;
    private bool isFlashing = false;

    void Awake()
    {
        sr = GetComponent<SpriteRenderer>();
    }

    public async void TriggerFlash()
    {
        if (isFlashing) return;

        isFlashing = true;
        for (int i = 0; i < flashCount; i++)
        {
            sr.color = new Color(1, 1, 1, 0); // transparente
            await Awaitable.WaitForSecondsAsync(flashDuration);
            sr.color = new Color(1, 1, 1, 1); // visible
            await Awaitable.WaitForSecondsAsync(flashDuration);
        }
        isFlashing = false;
    }
}
```


En ambos casos, para probarlo desde el PlayerController:

```csharp title="PlayerController.cs"
void Update()
{
    if (Input.GetKeyDown(KeyCode.D))
    {
        GetComponent<DamageEffectAsync>().TriggerFlash();
    }
}
```

### Ventajas de la versión async/await

! Moderno (``async/await``) | Clásico (IEnumerator) |
|------------------|---------------------|
| Llama como un método normal | Requiere ``StartCoroutine`` |
| Sintaxis clara y secuencial | Código algo más repetitivo |
| Ysa await Awaitable.WaitForSecondsAsync() | Usa ``yield return new WaitForSeconds()`` |
| Se integra naturalmente con ``Task+``, ``async``, I/O, etc. | Usa ``yield return`` |
| Más fácil de leer y mantener | Puede ser más difícil de seguir |
| Manejo de excepciones más sencillo | Manejo de excepciones más complicado |

Hay que tener en cuenta que:

- Aunque esta API es más moderna, todavía está en adopción progresiva y puede no estar tan bien documentada como IEnumerator.

- No todos los métodos de Unity son awaitable, pero puedes usarlo para delays, input, animaciones, transiciones, etc.