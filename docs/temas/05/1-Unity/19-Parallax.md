# **El efecto Parallax**

El efecto Parallax es una técnica visual que crea la ilusión de profundidad en un juego 2D al mover diferentes capas de fondo a diferentes velocidades. Esto da la sensación de que los objetos más cercanos se mueven más rápido que los objetos lejanos, lo que mejora la inmersión del jugador.

## **Ejemplo de Implementación**

Escenario típico:

- Tienes varias capas de fondo (montañas, árboles, cielo).

- Todas son imágenes tipo Sprite.

- El jugador se mueve lateralmente (como en un endless runner o plataforma 2D).

### Script de ejemplo

```csharp title=ParallaxLayer.cs
using UnityEngine;

public class ParallaxLayer : MonoBehaviour
{
    public Transform cameraTransform;
    public float parallaxFactor = 0.5f; // 0 = inmóvil, 1 = igual que la cámara

    private Vector3 previousCameraPosition;

    void Start()
    {
        if (cameraTransform == null)
            cameraTransform = Camera.main.transform;

        previousCameraPosition = cameraTransform.position;
    }

    void LateUpdate()
    {
        Vector3 delta = cameraTransform.position - previousCameraPosition;

        // Solo se mueve horizontalmente, para juegos side-scroller
        transform.position += new Vector3(delta.x * parallaxFactor, 0f, 0f);

        previousCameraPosition = cameraTransform.position;
    }
}
```

### ¿Cómo usarlo?

1. Prepara las capas de fondo

    - Crea un GameObject por cada capa de fondo: por ejemplo Sky, Mountains, Trees.

    - Asigna un sprite diferente a cada uno (pixel art, de preferencia con resolución ancha).

    - Posiciónalos en diferentes planos del eje Z (por ejemplo: Z = 10, 20, 30) para ordenar el renderizado.

2. Agrega el script ParallaxLayer

    - Añádelo a cada capa.

    - Configura un valor distinto de parallaxFactor:

        - Fondo más lejano: 0.1

        - Fondo medio: 0.5

        - Fondo cercano: 0.8

3. Asigna la cámara

    - Puedes dejar el campo cameraTransform vacío: el script toma Camera.main automáticamente.

### ¿Qué pasa cuando el jugador se mueve?

A medida que la cámara sigue al jugador, este script detecta el desplazamiento.

Cada capa se mueve en proporción a ese desplazamiento.

Así se crea el efecto visual de profundidad, aunque todo esté en 2D.

### Variantes
- Puedes usar diferentes ejes (X, Y) para juegos de desplazamiento vertical.    
- Puedes agregar efectos de escala o rotación para mayor complejidad.   
- Ejecto looping en el fondo: puedes usar un script adicional para hacer que las capas se repitan al salir de la vista de la cámara.    
- Puedes usar un shader para efectos de distorsión o color.
- Puedes combinarlo con efectos de partículas para mayor inmersión.